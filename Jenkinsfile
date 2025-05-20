pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
    }

    tools {
        python 'Python3'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/minnathdhani/CICD-Jenkins.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest test_app.py --maxfail=1 --disable-warnings -q'
            }
        }

        stage('Run Flask App') {
            steps {
                sh 'docker run -d 5000:5000 python:3.10 nohup python app.py &
'
            }
        }
    }
}

   post {
       success {
            mail to: 'dhaniminnath@gmail.com',
                 subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline completed successfully at ${env.BUILD_URL}"
        }
        failure {
            mail to: 'dhaniminnath@gmail.com',
                 subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Pipeline failed. Check logs at ${env.BUILD_URL}"
        }
    }

