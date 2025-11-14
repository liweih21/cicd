pipeline {
    agent any

    environment {
        WEBEX_WEBHOOK_URL = credentials('webex-webhook')  // Create this credential in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/<your-user>/<your-repo>.git'
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
                    url: "${WEBEX_WEBHOOK_URL}",
                    contentType: 'APPLICATION_JSON',
                    requestBody: """{
                        "markdown": "**Build succeeded** for job: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
                    }"""
            }
        }
        failure {
            script {
                httpRequest httpMode: 'POST',
                    url: "${WEBEX_WEBHOOK_URL}",
                    contentType: 'APPLICATION_JSON',
                    requestBody: """{
                        "markdown": "**Build FAILED** for job: ${env.JOB_NAME} (#${env.BUILD_NUMBER})"
                    }"""
            }
        }
    }
}
