pipeline {
    agent any

    environment {
        https://earless-kamryn-clingiest.ngrok-free.dev = credentials('admin')  // Create this credential in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/liweih21/cicd.git'
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
                    pytest --maxfail=1 --disable-warnings
                '''
            }
        }
    }

    post {
        success {
            script {
                httpRequest httpMode: 'POST',
                    url: "${https://earless-kamryn-clingiest.ngrok-free.dev}",
                    contentType: 'APPLICATION_JSON',
                    requestBody: """{
                        "markdown": "**Build succeeded** for job: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
                    }"""
            }
        }
        failure {
            script {
                httpRequest httpMode: 'POST',
                    url: "${https://earless-kamryn-clingiest.ngrok-free.dev}",
                    contentType: 'APPLICATION_JSON',
                    requestBody: """{
                        "markdown": "**Build FAILED** for job: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
                    }"""
            }
        }
    }
}
