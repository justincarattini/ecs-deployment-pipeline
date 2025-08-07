pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-2'
        AWS_ACCOUNT_ID = '137112412989'
        FRONTEND_REPO = 'frontend'
        BACKEND_REPO = 'backend'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 
'https://github.com/justincarattini/clean-ecs-deployment.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t frontend ./frontend'
                sh 'docker build -t backend ./backend'
            }
        }

        stage('Tag and Push to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION | docker login 
--username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                    docker tag frontend:latest 
$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$FRONTEND_REPO:latest
                    docker tag backend:latest 
$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$BACKEND_REPO:latest
                    docker push 
$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$FRONTEND_REPO:latest
                    docker push 
$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$BACKEND_REPO:latest
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                }
            }
        }
    }
}

