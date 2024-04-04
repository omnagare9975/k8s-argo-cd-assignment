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
- `image's`: Images for explanation approach 

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

![EC2 Setup](image's/Creating EC2 Instance (VM).png)








