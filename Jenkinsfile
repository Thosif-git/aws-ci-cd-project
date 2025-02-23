pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "571600848518.dkr.ecr.us-east-1.amazonaws.com/cicdpipeline"
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
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t cicdpipeline:latest .'
                }
            }
        }

        stage('Tag and Push Image to ECR') {
            steps {
                script {
                    sh 'docker tag cicdpipeline:latest $ECR_REPO:latest'
                    sh 'docker push $ECR_REPO:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}
