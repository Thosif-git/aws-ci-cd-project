pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'cicdpipeline'
        KUBECONFIG = credentials('kubeconfig-aws')  // Corrected reference to credentials
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Thosif-git/aws-ci-cd-project.git'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin 571600848518.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = "571600848518.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
                    sh "docker build -t ${imageTag} ."
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                script {
                    def imageTag = "571600848518.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
                    sh "docker push ${imageTag}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        export KUBECONFIG=${KUBECONFIG}
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
}
