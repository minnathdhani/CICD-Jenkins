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


### Screenshorts:

1. Jenkins dashboard showing pipeline success<br>
    <img width="942" alt="image" src="https://github.com/user-attachments/assets/e78de979-7daf-4bdc-8bc6-b026ed4e575b" /><br>
   
2. Jenkins pripeline overview<br>
    <img width="946" alt="image" src="https://github.com/user-attachments/assets/e3c78ef0-c691-4113-9ba8-6679682f6074" /><br>

4. Email notifications<br>
   Success Email 
   <br>
   
5. Application Screenshots<br>
   <img width="956" alt="image" src="https://github.com/user-attachments/assets/692fcdb4-db00-4f09-bfb3-804d6c6d2483" /><br>
   <img width="552" alt="image" src="https://github.com/user-attachments/assets/594a0b07-4a34-449d-901a-6eb4d7108f67" /><br>
   ![Screenshot (2)](https://github.com/user-attachments/assets/863b76d6-ddb4-47ec-b31e-c21d83c89e5d)<br>
   ![Screenshot (4)](https://github.com/user-attachments/assets/eb52872d-a0d0-4565-94d0-700c14668333)<br?
   


7. Adding new users<br>
   ![image](https://github.com/user-attachments/assets/43551600-18a1-453b-833f-ccc5db3bd75e)<br>
   <img width="628" alt="image" src="https://github.com/user-attachments/assets/571c085d-3d3c-45d2-bade-390f847a48a1" /><br>
   



