pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'cicdpipeline'
        KUBECONFIG = credentials('kubeconfig-aws')
    }

    stage('Checkout Code') {
    steps {
        git branch: 'main', url: 'https://github.com/Thosif-git/aws-ci-cd-project.git'
    }
}


        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = "571600848518.dkr.ecr.us-east-1.amazonaws.com/${ECR_REPO}:latest"
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin 571600848518.dkr.ecr.${AWS_REGION}.amazonaws.com
                        docker build -t ${imageTag} .
                        docker push ${imageTag}
                    """
                }
            }
        }

        stage('Apply Terraform') {
            steps {
                script {
                    sh '''
                        cd terraform
                        terraform init
                        terraform apply -auto-approve
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh '''
                        kubectl apply -f kubernetes/deployment.yaml
                        kubectl apply -f kubernetes/service.yaml
                    '''
                }
            }
        }
    }
}
