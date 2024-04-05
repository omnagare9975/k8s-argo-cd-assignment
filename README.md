# k8s-argo-cd-assignment
## Overview
This repository contains my solution for the DevOps internship assignment. The assignment required deploying a web application using Kubernetes and Argo CD, implementing a canary release strategy, and documenting the approach, challenges, and solutions.

## Folder Structure

The project is organized as follows:

- `app/`: Contains the application files.
  - `Dockerfile`: Used to build the Docker image.
  - `index.html`: Main HTML file for the application.
- `kubernetes/`: Kubernetes configuration files.
  - `manifests/`: Contains deployment and service configurations.
    - `deployment.yaml`: Deployment configuration.
    - `service.yaml`: Service configuration.
  - `argocd/`: Argo CD configuration.
    - `app.yaml`: Argo CD application definition.
- `README.md`: This file.
## Here Our Web-Application 
![Application](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/32c984f1-7ab1-4017-8136-3a8ea0a0abf0)


  ## Setup

To set up the project locally, follow these steps:

1. Clone the repository.
2. ```https://github.com/omnagare9975/k8s-argo-cd-assignment.git```
3. Navigate to the `app/` directory.
4. Build the Docker image using the provided `Dockerfile`.
5. Apply the Kubernetes manifests in the `kubernetes/manifests/` directory to your Kubernetes cluster.

## Step by Step Approach 
```Step1 : Setup and Configuration :- ```


- Create the virtual machine on AWS. Since I'm using Windows, I prefer to go with the cloud provider (AWS).
- Launching the EC2 instance with the Ubuntu image(os). We are selecting the `t2.large` instance type here because we are using `minikube` to create the Kubernetes environment locally, and 
  it requires `2cpu` and 20GB (storage) volume .
- SSH into the EC2 instance using `MobaXterm` or `putty`. I'm using the terminal because it's faster than these tools.

![Creating EC2 Instance (VM)](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/18d22209-2080-4e48-83c2-2a149aca3cbb)


- Now we have to install `Minikube` (Local Kubernetes Environment) and `Kubectl` (command-line tool) in our VM to run the Kubernetes Cluster.
- For running `Minikube`, we have to install `Docker` and select it as the driver for performance.
- Installation of Argo CD (it monitors your cluster and infrastructure stored in Git, and compares the two to automate application deployment) on the Kubernetes cluster.
- Then install an Argo Rollout (enables you to roll out new versions of your application in a controlled and automated manner).
- After configuration, we will watch for its state to check if any installation failed or if services or servers are running.

``` Step 2: Creating the GitOps Pipeline :-```


  Dockerize the Application

   
- We have already installed Docker, and now we need to build the image of our web application.
  - `app/`: Contains the application files.
  - `Dockerfile`: Used to build the Docker image.
  - `index.html`: Main HTML file for the application.

- In the `Dockerfile`, I'm using `nginx:alpine`, which is a lightweight image, and then copying the web application (`index.html`) into the nginx server.
  ![Build and Push Docker image](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/6d653bd2-fbc2-4ac8-baa5-82c3efd42b60)

- After building the image, I run it locally to check if it's working or not (shown in the above image), deployed on port `8000`.
- Then, I logged into DockerHub, which is a container registry, and pushed my application image (`web-portfolio-intern`) to it with the `:latest` version.

Deploy the Application Using Argo CD:-
- Now that we have pushed the image successfully, we need to modify our Kubernetes `manifests` file.
  - `kubernetes/`: Kubernetes configuration files.
  - `manifests/`: Contains deployment and service configurations.
    - `deployment.yaml`: Deployment configuration.
    - `service.yaml`: Service configuration.

- In `deployment.yml`, we need to add our Docker image that we have built and pushed to DockerHub: `(omnagare/web-portfolio-intern:latest)`.
- We have already installed Argo CD. Now, we need to get the Argo CD services, then the Argo CD server.

## Challenges
- While trying to access the Argo CD UI, I faced a problem because the Argo CD server was on `ClusterIP` (which allows clients to send requests to a stable internal IP address), but I wanted to access it on a web browser.
- I also faced another issue where the port was not accessible on my browser.

## Solution
- To access the Argo CD UI on my browser, I edited the services. I changed the service to `NodePort` (which allows clients to send requests to the IP address of a node on one or more nodePort values specified by the service). Then, I allowed traffic to the server by defining inbound rules in the security group for `HTTP` and `HTTPS`.
- After that, I used port forwarding because I am using an AWS EC2 VM. I forwarded port `:4444` on the server with my VM IPv4 address. This allowed me to successfully access the Argo CD UI. I used the UI, but I also provided the CLI YAML file.

Here is the configured setup -
![Sync and fetch Repo](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/fccb63cf-95e6-4237-86d4-97cc191ff95a)

- i have configured the GitHUB repo with Argo CD 
-  We have `2 replicas` here in `Deployment.yml` 
- For testing purposes, I scaled up the replicas to 3 in our VM, which caused it to go out of sync. However, I was able to synchronize the full application.
- I discovered that the application is fully configured with our GitHub repo.

### Step 3: Implementing a Canary Release with Argo Rollouts

- For the rollout strategy, we defined a canary release in the rollout definition.
- In our `Deployment.yml`, we defined it as follows:

```
strategy:
  canary:
    steps:
      - setWeight: 10
        pause: {}
      - setWeight: 50
        pause:
          duration: 30s
```

- We specified a canary release strategy where we gradually increased the weight of the version (`:latest` of our Docker image) in two steps: first to 10% and then to 50%, with a pause of 30 seconds between each step.

- To trigger a rollout, we updated our image from `:latest` to `:v1` by rebuilding the Docker image.
- After building the image and all necessary steps, we defined it in our manifests `deployment.yml`.

![Changes in repo Rollout](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/b928e534-ddfd-431d-b9c1-1f102613d13d)
![Version of app v1 and latest](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/c7c5646b-2f81-4b07-b42a-62c51da44092)

- We have successfully defined the canary release and changed the application version from `:latest` to `:v1` for the rollout.
- Using Argo Rollouts CLI, we monitored the changes of the rollout. The command `kubectl argo rollouts get rollout my-web-app -n default` provided detailed information about the rollout progress, including the current status and the number of replicas.

- We observed that the canary release steps were progressing as expected. The new version of the application was being gradually rolled out according to the defined canary release strategy, and our canary release successfully completed.

## Challenge
- While rolling out, my virtual machine went down due to some issue.
- This caused my synchronization to fail for our application that we recently changed from `:latest` to `:v1`.

## Solution
- I re-SSHed to my instance and tried the rollout again, this time it started working. I increased the terminal time to '30min' because it was going down from time to time.

## Clean Up
- Now we need to clean up the resources.
- First, we delete the Argo CD namespace using `kubectl delete namespace argocd`.
- Second, for the Argo Rollout object, we delete our object `my-web-app` using `kubectl delete rollout my-web-app`.
- Verify using `kubectl get all`.
- Then, we need to delete the Docker image that we built using `docker rmi web-portfolio-intern`.
- Finally, we stop the Minikube using `minikube stop`.
- Since I'm using the EC2 instance on AWS, I terminate it (VM).










