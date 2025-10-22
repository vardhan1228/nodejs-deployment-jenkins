# Jenkins Deployment Guide with GitHub Integration

This guide explains how to set up Jenkins on an AWS EC2 Linux instance and configure it with GitHub webhooks for automated builds.

## Prerequisites

- An AWS account
- A GitHub repository
- Basic knowledge of Linux commands
- SSH access to your EC2 instance

## AWS EC2 Setup

1. Launch an EC2 Instance:
   - Choose Amazon Linux 2 AMI
   - Recommended: t2.micro (free tier) or t2.small
   - Configure Security Group:
     ```
     Port 22 (SSH)     : Your IP
     Port 8080 (Jenkins): 0.0.0.0/0
     Port 80 (HTTP)    : 0.0.0.0/0
     Port 443 (HTTPS)  : 0.0.0.0/0
     ```

2. Connect to your EC2 instance:
   ```bash
   ssh -i your-key.pem ec2-user@your-instance-ip
   ```

## Jenkins Installation

1. Update the system:
   ```bash
   sudo yum update -y
   ```

2. Install Java:
   ```bash
sudo dnf install java-17-amazon-corretto -y
   ```

3. Add Jenkins repository:
   ```bash
   sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
   ```

4. Install Jenkins:
   ```bash
   sudo yum install jenkins -y
   ```

5. Start Jenkins:
   ```bash
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```

6. Get the initial admin password:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

## Jenkins Configuration

1. Access Jenkins:
   - Open your browser and navigate to: `http://your-ec2-ip:8080`
   - Enter the initial admin password
   - Install suggested plugins

2. Install Required Plugins:
   - Go to "Manage Jenkins" > "Manage Plugins"
   - Install these plugins:
     - GitHub Integration
     - Pipeline
     - Git
## Docker Installation and Configuration

1. Install Docker on EC2:
   ```bash
   # Install Docker
   sudo yum update -y
   sudo yum install docker -y
   sudo systemctl start docker
   sudo systemctl enable docker
   
   # Add jenkins user to docker group
   sudo usermod -aG docker jenkins
   sudo systemctl restart jenkins
   ```

2. Install Docker Pipeline Plugin in Jenkins:
   - Go to "Manage Jenkins" > "Manage Plugins"
   - Go to "Available" tab
   - Search for and install:
     - Docker Pipeline
     - Docker
     - Docker API

## Storing Docker Credentials in Jenkins

1. Create Docker Hub Access Token:
   - Log in to Docker Hub
   - Go to Account Settings > personal access token
   - Click "New Access Token"
   - Give it a name (e.g., "jenkins-access")
   - give readd and write 
   - Copy the token immediately

2. Add Docker Credentials to Jenkins:
   - Go to "Manage Jenkins" > "Manage Credentials"
   - Click on "(global)" domain
   - Click "Add Credentials"
   - Choose "secret text"
   - Set the following:
     - Kind: secret tesxt
     - Scope: Global
     - Password: Your Docker Hub access token
     - ID: dockerhub-token
     - Description: dockerhub-token

   ```
## GitHub Integration

3. Create a Pipeline:
   - Click "New Item"
   - Choose "Pipeline"
   - In Pipeline Configuration:
     - Check "GitHub Project" and add your repository URL
     - Under Build Triggers, check "GitHub hook trigger for GITScm polling"

4. Configure GitHub Webhook:
   - Go to your GitHub repository
   - Settings > Webhooks > Add webhook
   - Payload URL: `http://your-ec2-ip:8080/github-webhook/`
   - Content type: application/json
   - Select "Just the push event"
   - Save webhook

## Using the Jenkinsfile

The Jenkinsfile in this repository defines the pipeline stages for your build. Make sure it's in the root of your repository.

## Testing the Setup

1. Make a change to your repository
2. Push to GitHub
3. Jenkins should automatically trigger a build
4. Check the Console Output in Jenkins to monitor the build





