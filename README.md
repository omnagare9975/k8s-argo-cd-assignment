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
- After that, I used port forwarding because I am using an AWS EC2 VM. I forwarded port `:44444` on the server with my VM IPv4 address. This allowed me to successfully access the Argo CD UI. I used the UI, but I also provided the CLI YAML file.

Here is the configured setup -
![Sync and fetch Repo](https://github.com/omnagare9975/k8s-argo-cd-assignment/assets/118531541/fccb63cf-95e6-4237-86d4-97cc191ff95a)

- i have configured the GitHUB repo and we have `2 replicas` here in `Deployment.yml` 

    
     









