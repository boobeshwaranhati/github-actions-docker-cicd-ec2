# Automated Docker CI/CD Pipeline and AWS EC2 Deployment Using GitHub Actions

## 1. Project Overview

**Task Name:** Automated Docker CI/CD Pipeline and AWS EC2 Deployment Using GitHub Actions

**Status:** Completed

Implemented an automated CI/CD pipeline using GitHub Actions to build, publish, and deploy a containerized HTML website to AWS EC2.

Whenever code is pushed to the `main` branch of the GitHub repository, GitHub Actions automatically validates the website files, builds a Docker image, pushes the image to Docker Hub, and deploys the latest image to an EC2 instance.

The pipeline was successfully tested on both Amazon Linux 2023 and Ubuntu EC2 instances.

## 2. Objectives

- Create a basic HTML website.
- Containerize the website using Docker.
- Store the application source code in GitHub.
- Automate website file validation using GitHub Actions.
- Build a Docker image automatically when code is pushed to the `main` branch.
- Push the Docker image to Docker Hub.
- Deploy the latest Docker image to AWS EC2 using SSH.
- Verify the deployment on Amazon Linux 2023 and Ubuntu.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| HTML | Develop the website |
| Docker | Containerize the website |
| Nginx | Serve the HTML website |
| Git | Version control |
| GitHub | Source code management |
| GitHub Actions | Automate the CI/CD pipeline |
| Docker Hub | Store Docker images |
| AWS EC2 | Host the application |
| SSH | Connect to EC2 for deployment |
| Linux | Run Docker containers |
| YAML | Define the GitHub Actions workflow |

## 4. Project Architecture

The following diagram illustrates the complete CI/CD workflow, from pushing code to GitHub to deploying the Docker container on AWS EC2.

![Automated Docker CI/CD Pipeline Architecture](Automated%20Docker%20CI_CD%20Pipeline%20Architecture.png)

## 5. Implementation Steps

### Step 1: Create the HTML Website
---

Created a basic HTML website using `index.html`.

The HTML file contains the website content served by the Nginx web server.


### Step 2: Create the Dockerfile
---
Created a Dockerfile to package the website into a Docker image.

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```
---
Explanation:
1. FROM nginx:alpine uses the lightweight Nginx image as the base image.
2. COPY copies the HTML file into the Nginx web directory.
3. EXPOSE 80 documents the port used by the web server.

### Step 3: Push the Project to GitHub
---
Uploaded the application source code and Dockerfile to the GitHub repository.

GitHub Repository: https://github.com/boobeshwaranhati/github-actions-docker-cicd-ec2

Configured the workflow to execute automatically whenever code is pushed to the main branch.
### Step 4: Configure Docker Hub
---
Configured Docker Hub authentication using GitHub repository secrets.

The Docker image is published with the following name:

boobesh2005/github-actions-docker-cicd-ec2:latest

Docker Hub stores the image so that the EC2 instance can pull and run it during deployment.

Docker Hub Repository: https://hub.docker.com/r/boobesh2005/github-actions-docker-cicd-ec2

### Step 5: Configure AWS EC2
---
Configured AWS EC2 to host the containerized HTML website.

Tested the deployment on the following operating systems:

Amazon Linux 2023
Ubuntu Server

## AWS IAM Configuration
| Secret Name             | Purpose                                      |
| ----------------------- | -------------------------------------------- |
| `AWS_ACCESS_KEY_ID`     | Authenticates the IAM user with AWS          |
| `AWS_SECRET_ACCESS_KEY` | Provides the corresponding secret access key |
| `DOCKERHUB_USERNAME`    | Docker Hub authentication                    |
| `DOCKERHUB_TOKEN`       | Authenticates Docker Hub image publishing    |
| `EC2_SSH_KEY`           | Private SSH key for connecting to EC2        |
---
The EC2 key pair name mumbai was configured in the workflow for launching the EC2 instance.

## EC2 Network Configuration

The GitHub Actions workflow automatically discovers the default VPC and subnet, creates a security group, and launches a new EC2 instance.

The security group allows:

Port 22: SSH access for remote deployment.
Port 80: HTTP access to the website.

The workflow automatically retrieves the public IP address of the newly launched EC2 instance and uses it for deployment and website verification.

Created an AWS IAM user with administrator access for the project setup and automated EC2 provisioning. Generated the IAM user's access keys and stored them as GitHub repository secrets to allow GitHub Actions to authenticate with AWS.

The following GitHub Secrets were configured:

### Step 6: Configure GitHub Actions
---
# Authentication and Deployment

| Authentication | Purpose |
|---|---|
| AWS IAM credentials | Authenticate GitHub Actions with AWS to create EC2 resources automatically. |
| EC2 SSH private key | Connect to the newly created EC2 instance and deploy the Docker container. |
| Docker Hub credentials | Authenticate with Docker Hub to publish the Docker image. |

GitHub Actions uses credentials stored in GitHub Secrets to authenticate with AWS and Docker Hub. It automatically creates an EC2 instance, detects its public IP address, connects through SSH, deploys the latest Docker image, and verifies website accessibility through port 80.
AWS Authentication

GitHub Actions authenticates with AWS using access keys associated with the configured IAM user. The credentials are stored securely in GitHub repository secrets and are not hardcoded in the workflow.

The aws-actions/configure-aws-credentials action configures AWS credentials for subsequent AWS CLI commands.

## Automated EC2 Provisioning

After successfully building and publishing the Docker image, the workflow:

Discovers the default VPC and subnet.
Creates a security group with the required inbound rules.
Launches a t3.micro EC2 instance using the configured AMI and key pair.
Waits for the instance to enter the running state.
Automatically retrieves the instance's public IP address.
Waits for SSH connectivity before starting deployment.
Remote Deployment Using SSH

The appleboy/ssh-action@v1.2.0 action connects to the newly created EC2 instance using its public IP address and the private key stored in EC2_SSH_KEY.

The workflow installs Docker when required, pulls the latest image from Docker Hub, replaces the previous application container, and starts the updated container with port 80 exposed.

Finally, the workflow checks HTTP accessibility to confirm successful deployment.
Created a GitHub Actions workflow file at:

.github/workflows/docker-cicd.yml

## The workflow contains the following stages:

1. Checkout Code

Uses actions/checkout@v4 to download the repository source code into the GitHub-hosted runner.

2. Validate Website

Checks whether index.html and Dockerfile exist and verifies that the HTML file contains an HTML tag.

3. Login to Docker Hub

Authenticates with Docker Hub using credentials stored in GitHub repository secrets.

4. Build and Push Image

Builds the Docker image and pushes it to Docker Hub using docker/build-push-action@v6.

5. Deploy to EC2

Uses appleboy/ssh-action@v1.2.0 to connect to the EC2 instance through SSH.

The deployment commands install Docker if required, start the Docker service, pull the latest image, remove the previous application container, and start the updated container.

### Step 7: Deploy on Amazon Linux 2023
---
Used Amazon Linux-compatible commands to install and start Docker.

sudo dnf install -y docker
sudo systemctl enable --now docker

Pulled the latest Docker image:

sudo docker pull boobesh2005/github-actions-docker-cicd-ec2:latest

Removed the previous container and started the updated container:

sudo docker rm -f html-website || true

sudo docker run -d \
  --name html-website \
  --restart unless-stopped \
  -p 80:80 \
  boobesh2005/github-actions-docker-cicd-ec2:latest

Result: Deployment completed successfully on Amazon Linux 2023.

### Step 8: Deploy on Ubuntu
---
Updated the deployment commands to use Ubuntu-compatible package management and the correct SSH username.

Installed and started Docker:

sudo apt-get update
sudo apt-get install -y docker.io
sudo systemctl enable --now docker

Pulled the latest image from Docker Hub, removed the previous application container, and started the updated container.

Used the same Docker image and container configuration as the Amazon Linux deployment.

Result: Deployment completed successfully on Ubuntu EC2.

### Step 9: Verify the Deployment
---
Verified the running Docker container using:

sudo docker ps

Confirmed that the container was running and that EC2 port 80 was mapped to container port 80.

The website was accessed using the public IP address of the EC2 instance:

http://<EC2-PUBLIC-IP>

Confirmed that the website was accessible after deployment on both Amazon Linux 2023 and Ubuntu.

## 6. Testing and Results

| Test Case                         | Expected Result                            | Actual Result |
| --------------------------------- | ------------------------------------------ | ------------- |
| Push code to the main branch      | GitHub Actions workflow starts             | Passed        |
| Validate website files            | Required files and HTML tag are checked    | Passed        |
| Authenticate with Docker Hub      | Authentication succeeds                    | Passed        |
| Build Docker image                | Image builds successfully                  | Passed        |
| Push Docker image                 | Image is published to Docker Hub           | Passed        |
| Connect to Amazon Linux using SSH | SSH connection succeeds                    | Passed        |
| Deploy on Amazon Linux            | Container starts successfully              | Passed        |
| Connect to Ubuntu using SSH       | SSH connection succeeds                    | Passed        |
| Deploy on Ubuntu                  | Container starts successfully              | Passed        |
| Verify container status           | Container is running                       | Passed        |
| Verify website access             | Website is accessible through HTTP port 80 | Passed        |

### Build Status

The following image shows the build status of the GitHub Actions CI/CD pipeline.

![GitHub Actions Build Status](Build%20Status.png)

Overall Result: All implemented pipeline stages and both EC2 deployment tests completed successfully.

## 7. Security Considerations

Stored AWS IAM access keys and Docker Hub credentials in GitHub repository secrets instead of hardcoding them in the workflow.
Used an IAM user with administrator access for the project's AWS resource provisioning.
Used SSH key-based authentication for remote EC2 deployment.
Configured security group rules for SSH and HTTP traffic.
Used GitHub Actions to automate AWS resource creation and deployment.
Used the EC2_SSH_KEY secret to authenticate SSH connections without exposing the private key in the repository.

Security improvement: Administrator access grants broad permissions. For a production implementation, replace it with a dedicated IAM policy granting only the permissions required by the workflow. SSH access should also be restricted to trusted source IP addresses rather than opened to the entire internet.

## 8. Key Learnings

Understanding the CI/CD workflow using GitHub Actions.
Automating application validation, Docker image builds, and image publishing.
Deploying Docker containers to AWS EC2 through SSH.
Understanding Docker image and container management.
Configuring port mapping between EC2 and Docker containers.
Using operating-system-specific commands to install Docker on Amazon Linux and Ubuntu.
Managing deployment credentials through GitHub repository secrets.
Verifying successful deployments using Docker commands and browser testing.

## 9. Conclusion

Successfully implemented an automated Docker CI/CD pipeline using GitHub Actions, Docker Hub, and AWS EC2.

The pipeline automatically validates the application files, builds and publishes the Docker image, and deploys the latest image to EC2 whenever code is pushed to the main branch.

The implementation was tested successfully on both Amazon Linux 2023 and Ubuntu EC2 instances.

This project demonstrates the practical application of CI/CD automation, Docker containerization, container image management, SSH-based deployment, and AWS EC2 hosting.

## 10. Final Status

Task Status: Completed

Deployment Status: Successful

Amazon Linux 2023 Test: Passed

Ubuntu Test: Passed

GitHub Actions Pipeline: Successful

Docker Hub Image Build and Push: Successful
