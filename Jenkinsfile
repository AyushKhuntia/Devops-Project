pipeline {
    agent any

    tools {
        maven 'Maven-3.9'
    }

    environment {
        DOCKER_IMAGE = 'rfq-auction'
        K8S_DEPLOYMENT_FILE = 'k8s-deployment.yaml'
        KUBECONFIG = 'C:/Users/hp/.kube/config'
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

        stage('Load Image to Kind') {
            steps {
                bat "docker save ${DOCKER_IMAGE}:latest -o rfq-auction.tar"
                bat "docker exec -i desktop-worker ctr -n k8s.io images import - < rfq-auction.tar"
                bat "docker exec -i desktop-worker2 ctr -n k8s.io images import - < rfq-auction.tar"
                bat "docker exec -i desktop-control-plane ctr -n k8s.io images import - < rfq-auction.tar"
                bat "del rfq-auction.tar"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    if (isUnix()) {
                        sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                        sh "kubectl rollout restart deployment/rfq-auction-deployment"
                    } else {
                        bat "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                        bat "kubectl rollout restart deployment/rfq-auction-deployment"
                    }
                }
            }
        }
    }
}
