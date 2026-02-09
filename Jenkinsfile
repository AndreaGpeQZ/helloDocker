pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'andreagpeqz'
        DOCKERHUB_CREDENTIALS = 'docker_hub'       // ID de tus credenciales en Jenkins
        KUBECONFIG_ID = 'kubeconfig_minikube'      // ID de tu credencial de Minikube
        IMAGE_NAME = 'hellodocker'
        GIT_REPO = 'https://github.com/andreagpeqz/helloDocker.git' // Tu fork
    }

    stages {
        stage('Checkout GitHub repo') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: "${GIT_REPO}"]]
                )
            }
        }

        stage('Build Docker Image in Minikube') {
            steps {
                script {
                    // Configura Docker para usar el de Minikube
                    sh 'eval $(minikube -p minikube docker-env)'

                    // Construye y etiqueta la imagen
                    sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: "${DOCKERHUB_CREDENTIALS}", variable: 'DOCKERHUB_PASSWORD')]) {
                        sh "echo ${DOCKERHUB_PASSWORD} | docker login -u ${DOCKERHUB_USER} --password-stdin"
                    }

                    sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                script {
                    kubernetesDeploy(
                        configs: 'deploymentsvc.yaml',
                        kubeconfigId: "${KUBECONFIG_ID}"
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
