pipeline {
    agent any

    stages {

	 stage('Clone Repo') {
            steps {
                git branch: 'main', url:'https://github.com/minnathdhani/CICD-Jenkins.git'
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


         stage('Run Flask App') {
            steps {
               sh '''
                  docker run -d \
                  --name flask-app \
                  -p 5001:5000 \
                  -v $(pwd):/app \
                  -w /app \
                  python:3.10 \
                  bash -c "pip install -r requirements.txt && python app.py"
            '''
            echo "Application running on port ${port}"
        }
    }


            
        
    }
}

   post {
       success {
            mail to: 'minnathdhani@gmail.com',
                 subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline completed successfully at ${env.BUILD_URL}"
        }
        failure {
            mail to: 'minnathdhani@gmail.com',
                 subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline failed. Check logs at ${env.BUILD_URL}"
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

