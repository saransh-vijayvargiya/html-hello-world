**Report: Task 2 — Creating a Simple Jenkins Pipeline for CI/CD
Objective
To automate the process of building and deploying a static HTML application using Jenkins and Docker on an AWS EC2 instance through a CI/CD pipeline.
**


**Tools Used**
Jenkins (installed on EC2)
Docker (installed on EC2)
AWS EC2 instance (Ubuntu)
GitHub (code repository)
Docker Hub (container image registry)


**Step-by-Step Process**
Step 1: Prepare the Project
Developed a simple static HTML application.
Created a Dockerfile to containerize the app with Nginx as the web server.
Created a Jenkinsfile with defined pipeline stages for build, push, and deploy.

Step 2: Push Project to GitHub
Initialized a Git repository in the project folder.
Added the GitHub remote URL.
Committed and pushed the code including Dockerfile and Jenkinsfile to GitHub.

Step 3: Set Up Jenkins on EC2
Installed Jenkins on the EC2 Ubuntu instance.
Installed Docker on the EC2 instance.
Added the Jenkins user to the Docker group to allow Docker commands inside Jenkins jobs.
Added Docker Hub credentials to Jenkins credential manager securely.

Step 4: Configure Jenkins Pipeline Job
Created a Jenkins Pipeline job and linked it to the GitHub repository.
Configured Jenkins to pull the Jenkinsfile from the repo.
Configured Jenkins to trigger builds on code commits (can be manual or automated with webhooks).

Step 5: Run and Test Pipeline
Triggered the pipeline manually from Jenkins dashboard.

Verified stages:
Checkout: Successfully pulled source code from GitHub.
Docker Build: Built Docker image locally on EC2.
Docker Push: Pushed image to Docker Hub with credentials.
Deploy: Ran the Docker container on EC2 and mapped container port 80 to host port.

Step 6: Access and Verify Deployment
Checked running container with docker ps.
Accessed the deployed app in a web browser via http://<EC2-Public-IP>:<mapped-port>.
Optionally, re-ran container to map directly to port 80 for easier access.

Have added the SS as well
