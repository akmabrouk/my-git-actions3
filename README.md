# my-git-actions3

ChatGPT search: An example of dockerfile docker-image-build.yml that is built and pushed

Here’s a complete example of a Dockerfile and a GitHub Actions workflow (docker-image-build.yml) that builds and pushes the Docker image to Docker Hub.

1. Dockerfile Example
This Dockerfile is for a basic Node.js application. It installs dependencies and sets up a simple server.  (see the sample Dockerfile code)

Example Project Structure
-------------------------
my-node-app/
├── app.js
├── Dockerfile
├── package.json
└── package-lock.json


app.js (sample Node.js server):  (see the sample app.js code)

2. GitHub Actions Workflow: docker-image-build.yml
This GitHub Actions workflow builds the Docker image using the Dockerfile above and then pushes it to Docker Hub.

.github/workflows/docker-image-build.yml  (see the sample code)


Explanation of the Workflow
---------------------------
1. Trigger: The workflow triggers on any push to the main branch.
2. Jobs:
    Check out code: Uses the actions/checkout action to pull the code from your repository.
    Log in to Docker Hub: Logs into Docker Hub using credentials stored in GitHub secrets (DOCKER_USERNAME and DOCKER_PASSWORD).
    Set up Docker Buildx: Configures Docker Buildx, which is required to build and push images in GitHub Actions.
    Build and Push Docker Image: Uses docker/build-push-action to build and push the Docker image. The context is set to the current directory, and tags is set to akmabrdockerid/my-node-app:latest. Change this to your Docker Hub username and repository.
    Log out from Docker Hub: Logs out from Docker Hub as a cleanup step.

Setting Up GitHub Secrets
-------------------------
To store your Docker Hub credentials securely in GitHub:

1. Go to Settings > Secrets and Variables > Actions > Variables tab > Click New repository variable > Enter variable name as 
   DOCKER_USERNAME and value as akmabrdockerid
   Under Secrets tab > Click New repository Secret > Enter Secret Name as DOCKER_PASSWORD and Your Docker Hub password or access token as Secret Value. Refere to this for how to create Docker access tokens https://app.docker.com/settings/personal-access-tokens
2. Add the following secrets:
    DOCKER_USERNAME: Your Docker Hub username.
    DOCKER_PASSWORD: Your Docker Hub password or access token.

This setup will build and push the Docker image to Docker Hub each time there’s a push to the main branch. You can adjust the workflow and tags to fit your specific needs.

To setup Docker Token, go to top right of Docker login where you see 'A', Account Settings, Personal access tokens, 'generate new token' button
copy and paste the generated token to Github DOCKER_PASSWORD under Settings, Secrets and Variables > Actions 

Once the Docker image is created and pushed to Dockerhub, create deployment and service on minikube cluster my-cluster as follows:

abdel@My-Dell:~/repos/my-git-actions3$ kubectl apply -f deployment.yaml 

Using curl to run app on CLI use:
---------------------------------
1. Get one of cluster nodes IP address using: 
abdel@My-Dell:~/repos/my-git-actions3$ kubectl get nodes -o wide

2. User curl to test:
abdel@My-Dell:~/repos/my-git-actions3$ curl http://192.168.49.2:30080
Hello from Docker!


How to run the app on browser
-----------------------------

Note: Browser cannot connect using http://192.168.49.2:30080 — often due to networking, not the app
You're not using the same network
On Linux or Windows WSL2, 192.168.49.3 might not be reachable by the browser because Minikube runs in a VM
The solution is to use Port Forwarding as follows:

abdel@My-Dell:~/repos/my-git-actions3$ kubectl port-forward service/nodejs-service 3000:80
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
Handling connection for 3000

Go to browser and paste: http://localhost:3000
Hello from Docker!