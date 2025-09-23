pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-angular-app"
        IMAGE_TAG = "latest"
        ACR_LOGIN_SERVER = "acrtemp1.azurecr.io"  // Your ACR login server
        K8S_MANIFEST_PATH = "manifests"            // Updated folder name
        KUBECONFIG = "/var/lib/jenkins/.kube/config" // Jenkins node kubeconfig
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hamaadh824/dockerfile.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f Dockerfile ."
            }
        }

        stage('Login to ACR') {
            steps {
                withCredentials([usernamePassword(credentialsId: '38362803-e3bd-4bf2-bbbb-8a8326800f90',
                                                usernameVariable: 'ACR_USER',
                                                passwordVariable: 'ACR_PASS')]) {
                    sh "docker login ${ACR_LOGIN_SERVER} -u $ACR_USER -p $ACR_PASS"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to AKS') {
            steps {
                script {
                    // Ensure kubeconfig exists on Jenkins node
                    sh "export KUBECONFIG=${KUBECONFIG}"
                    
                    // Optional: Update manifests with new image tag
                    sh "sed -i 's|image:.*|image: ${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${IMAGE_TAG}|' ${K8S_MANIFEST_PATH}/*.yaml"
                    
                    // Apply manifests
                    sh "kubectl apply -f ${K8S_MANIFEST_PATH}/"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Docker image built, pushed to ACR, and deployed to AKS successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
