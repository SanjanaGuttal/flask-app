pipeline {
    agent any

    environment {
        AWS_REGION = "us-west-1"
        ECR_REPO_NAME = "qb-ecr-repo"
        IMAGE_TAG = "latest"
        AWS_ACCOUNT_ID = "705211342885"
        ECR_IMAGE_URL = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${ECR_IMAGE_URL}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh "docker push ${ECR_IMAGE_URL}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                script {
                    sh '''
                    aws ecs update-service \
                      --cluster flask-app-cluster \
                      --service flask-app-service \
                      --force-new-deployment \
                      --region ${AWS_REGION}
                    '''
                }
            }
        }
    }
}
