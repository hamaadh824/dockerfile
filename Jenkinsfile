pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-dotnet-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamaadh824/dockerfile.git',
                    credentialsId: 'github-credentials-id'
            }
        }

        stage('Build .NET Docker Image') {
            steps {
                script {
                    // Docker build
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f Dockerfile ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    // Optional: run container and test if needed
                    sh "docker run --rm ${IMAGE_NAME}:${IMAGE_TAG} dotnet --info"
                }
            }
        }

        stage('Archive Docker Info') {
            steps {
                archiveArtifacts artifacts: '**/Dockerfile', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Build successful!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
