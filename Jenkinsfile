pipeline {
    agent any

    environment {
        IMAGE_NAME = "andreagpeqz/hellodocker"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AndreaGpeQZ/helloDocker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    echo "Logging in to Docker Hub..."
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    echo "Pushing Docker image..."
                    bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                echo "Deploying to Minikube..."
                bat "minikube kubectl -- apply -f deploymentsvc.yaml"
            }
        }
    }

    post {
        always {
            echo "Pipeline finished."
        }
    }
}
