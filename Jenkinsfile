pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rfq-auction'
        K8S_DEPLOYMENT_FILE = 'k8s-deployment.yaml'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'mvn clean package -DskipTests'
                    } else {
                        bat 'mvn clean package -DskipTests'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh "docker build -t ${DOCKER_IMAGE}:latest ."
                    } else {
                        bat "docker build -t ${DOCKER_IMAGE}:latest ."
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    if (isUnix()) {
                        sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                    } else {
                        bat "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                    }
                }
            }
        }
    }
}
