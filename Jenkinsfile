pipeline {
    agent any
     
    environment {
        GITREPO = "https://github.com/minnathdhani/CICD-Jenkins.git"
        GIT_BRANCH = "main"
        EC2_SSH = "minnath-ec2"
        EC2_USER = "ubuntu"
        EC2_HOST = "15.206.127.159"
        CONTAINER_NAME = "minnath-flask-cicd"
        DOCKER_IMAGE = "minnathdhani/flask"
        DOCKER_CREDENTIALS_ID = "minnath-docker-cred"
        DOCKER_REGISTRY = "https://index.docker.io/v1"
        DOCKER_NETWORK = "app-network"
     }


    stages {
        stage('Get Build Number') { 
            steps {
                echo "Build Number: ${BUILD_NUMBER}"
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GITREPO}"
            }
        }


         stage('Install Dependencies') {
            steps {
	       sh '''
		          python3 -m venv venv
		          . venv/bin/activate
		          pip3 install -r requirements.txt
	        '''
             
            }
        }

         stage('Run Tests') {
	    steps {
		sh '''
                  . venv/bin/activate
                  if [ -d "tests" ]; then
		      echo "Running tests..."
                      pytest tests/
                  else
                      echo "No tests found"
                 fi
        '''
              }
        }


        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Test Docker Credentials') {
            steps {
                script {
                    docker.withRegistry("${DOCKER_REGISTRY}", "${DOCKER_CREDENTIALS_ID}") {
                        echo "Docker credentials are valid."
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}:${BUILD_NUMBER}").push()
                    }
                }
            }
        }

       stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ['minnath-ec2']) {
                    sh """
                        echo "Deploying to EC2..."

                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                            echo "Checking if Docker network ${DOCKER_NETWORK} exists..."
                            docker network inspect ${DOCKER_NETWORK} > /dev/null 2>&1 || docker network create ${DOCKER_NETWORK}
  
                            echo "Pulling Flask Application Image..."
                            docker pull ${DOCKER_IMAGE}:${BUILD_NUMBER}

                            echo "Stopping existing Flask container if running..."
                            docker stop ${CONTAINER_NAME} || true
                            docker rm ${CONTAINER_NAME} || true

                            echo "Starting new Flask container connected to network ${DOCKER_NETWORK}..."
                            docker run -d --name ${CONTAINER_NAME} --network ${DOCKER_NETWORK} \
                                -p 8000:8000 \
                                -e JWT_SECRET_KEY=minnathdhani \
                                -e MONGO_DB_NAME=flask_db \
                                ${DOCKER_IMAGE}:${BUILD_NUMBER}

                            echo "Deployment done."
                        '
                    """
               }
            }
        }
        
    }
}

   post {
        failure {
            echo 'Build or test failed. Sending notifications...'
            emailext(
    subject: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
    body: """\
<html>
<body>
<h3>Flask Application Build FAILED ❌</h3>
<p><strong>Job:</strong> ${env.JOB_NAME}</p>
<p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
<p><strong>Branch:</strong> ${env.GIT_BRANCH}</p>
<p><strong>Git Repo:</strong> ${env.GITREPO}</p>
<p><strong>Docker Image:</strong> ${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}</p>
<p><strong>EC2 Host:</strong> ${env.EC2_HOST}</p>
<p><strong>Failure Time:</strong> ${new Date().format("yyyy-MM-dd HH:mm:ss", TimeZone.getTimeZone("Asia/Kolkata"))}</p>
<p><a href="${env.BUILD_URL}">Click here to view full build logs</a></p>
</body>
</html>
""",
    mimeType: 'text/html',
    to: "${ALERT_EMAIL}"
)

        }


        success {
            echo 'Build and deployment passed successfully!'
            emailext(
    subject: "✅ Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
    body: """\
<html>
<body>
<h3>Flask Application Build & Deployment SUCCEEDED ✅</h3>
<p><strong>Job:</strong> ${env.JOB_NAME}</p>
<p><strong>Build Number:</strong> ${env.BUILD_NUMBER}</p>
<p><strong>Branch:</strong> ${env.GIT_BRANCH}</p>
<p><strong>Git Repo:</strong> ${env.GITREPO}</p>
<p><strong>Docker Image:</strong> ${env.DOCKER_IMAGE}:${env.BUILD_NUMBER}</p>
<p><strong>Deployed To:</strong> EC2 (${env.EC2_HOST})</p>
<p><strong>Deployment Time:</strong> ${new Date().format("yyyy-MM-dd HH:mm:ss", TimeZone.getTimeZone("Asia/Kolkata"))}</p>
<p><a href="${env.BUILD_URL}">Click here to view full build logs</a></p>
</body>
</html>
""",
    mimeType: 'text/html',
    to: "${ALERT_EMAIL}"
)

       }

        always {
            sh '''
                 # Clean up containers
                 docker ps -aq --filter "name=flask-app" | xargs -r docker stop || true
                 docker ps -aq --filter "name=flask-app" | xargs -r docker rm || true
            
           	 # Clean up dangling images
                 docker image prune -f
             '''
        }
    }
