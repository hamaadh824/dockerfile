pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-angular-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamaadh824/dockerfile.git',
                    credentialsId: 'github-credentials-id' // optional if public repo
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f Dockerfile ."
                }
            }
        }

        stage('Archive Dockerfile') {
            steps {
                archiveArtifacts artifacts: 'Dockerfile', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Docker build successful!'
        }
        failure {
            echo '❌ Docker build failed!'
        }
    }
}
