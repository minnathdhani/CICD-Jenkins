# Jenkins CI/CD Pipeline for Flask Application

## Objective
Set up a Jenkins pipeline to automate the build, test, and deployment of a simple Python Flask web application.

## Project Overview
This project demonstrates how to configure and run a CI/CD pipeline using Jenkins to:
- Install dependencies
- Run unit tests with `pytest`
- Deploy the application to a staging environment
- Trigger builds automatically upon code changes
- Notify team members via email about pipeline status
---

## Sample Flask Application Repository
Fork this sample Flask app to begin:  
🔗 [Sample Python Flask App](https://github.com/mohanDevOps-arch/Student_App)
---

## Setup
### 1. Jenkins Installation
- **Option 1**: Install Jenkins on a local or cloud-based VM (e.g., AWS EC2, DigitalOcean, GCP Compute Engine).
- **Option 2**: Use a cloud-based Jenkins service (e.g., CloudBees, Jenkins X).

Follow the official [Jenkins Installation Guide](https://www.jenkins.io/doc/book/installing/).

### 2. Install Required Tools on Jenkins Server
```bash
sudo yum update
sudo yum install python3 python3-pip
pip3 install virtualenv pytest
```

### 3. Install Jenkins Plugins<br>
Install the following plugins from Manage Jenkins > Plugin Manager:<br>
1. Git
2. Pipeline
3. Email Extension

### 4. Add MongoDB URI as Jenkins Credential
1. Go to Jenkins Dashboard > Manage Jenkins > Credentials
2. Choose the domain (usually (global))
3. Click Add Credentialsbr<>
   Kind: Secret text<br>
   Secret: Your MongoDB URI (e.g., mongodb+srv://user:pass@cluster.mongodb.net/db)<br>
   ID: Minnath-MONGO-URI (must match the ID used in Jenkinsfile)<br>
   Description: MongoDB connection string<br>

### 5. Add EC2 SSH Private Key in Jenkins
1. Go to Jenkins Dashboard > Manage Jenkins > Credentials
2. Select the appropriate domain (e.g., (global))
3. Click Add Credentials<br>
   Kind: SSH Username with private key<br>
   Username: ec2-user (or whatever your EC2 login username is)<br>
   Private Key: Choose "Enter directly", and paste your .pem key (e.g., minnath-tm.pem)<br>
   ID: minnathdhani-ec2 (you will use this ID in Jenkinsfile)<br>
---

## Triggers
### GitHub Webhook
1. Go to your GitHub repository: Settings > Webhooks
2. Click Add Webhook:<br>
   Payload URL: http://<JENKINS_URL>/github-webhook/<br>
   Content type: application/json<br>
   Trigger: Just the push event<br>

### Jenkins Configuration
1. In your Jenkins project:<br>
   Check Build Triggers > “GitHub hook trigger for GITScm polling”

### Notifications
1. Go to Manage Jenkins > Configure System
2. Set up SMTP under Email Notification
3. Configure Email Extension Plugin:
   Email notifications are handled in the post block of your Jenkinsfile
---

## Documentation
### Prerequisites
1. Python 3.x and pip
2. Jenkins installed and running
3. Jenkins plugins: Git, Pipeline, Email Extension
4. GitHub repository with Flask app and Jenkinsfile

### Submission Checklist
1. Forked GitHub repo with Flask app and Jenkinsfile
2. Jenkins pipeline with build, test, and deploy stages
3. Email notifications on build success/failure
4. GitHub webhook triggers on push to main
5. Screenshots showing each pipeline stage (build, test, deploy)
