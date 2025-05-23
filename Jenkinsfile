pipeline {
    agent any

    stages {

	 stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    
    post {
        always {
            echo 'Cleanup or notifications...'
        }
    }

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
      			  pip install -r requirements.txt
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
                  docker run -d -p 5050:5000 \
		  -v /var/lib/jenkins/workspace/minnath-flask-cicd:/app \
                  -w /app python:3.10-slim \
		  bash -c "pip install -r requirements.txt && python app.py"
            '''
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
    }

