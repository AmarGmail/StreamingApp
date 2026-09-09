pipeline {
    agent any

    environment {
        // Define environment variables here
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '065194293675'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        //IMAGE_TAG = 'jenkins-${BUILD_NUMBER}'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                    # Authenticate with AWS ECR
                    aws ecr get-login-password --region ${AWS_REGION} |
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Build and push AUTH Docker image') {
            steps {
                script {
                    // Build the Docker image
                    sh '''
                    
                    # Build the Docker image for streaming-auth
                    docker build \
                    -t ${ECR_REGISTRY}/streaming-auth:jenkins-${BUILD_NUMBER} \
                    -f ./backend/authService/Dockerfile \
                    ./backend/authService

                    #Push to ECR
                    docker push ${ECR_REGISTRY}/streaming-auth:jenkins-${BUILD_NUMBER}
                    '''   
                }
            }
        }

        stage('Build and push Streaming Docker image') {
            steps {
                script {
                   sh '''

                    docker build \
                    -t ${ECR_REGISTRY}/streaming-streaming:jenkins-${BUILD_NUMBER} \
                    -f ./backend/streamingService/Dockerfile \
                    ./backend

                    docker push ${ECR_REGISTRY}/streaming-streaming:jenkins-${BUILD_NUMBER}
                   ''' 
                }
            }
        }

        stage('Build & Push Admin') {
            steps {
                sh '''
                    docker build \
                      -t ${ECR_REGISTRY}/streaming-admin:jenkins-${BUILD_NUMBER} \
                      -f ./backend/adminService/Dockerfile \
                      ./backend

                    docker push ${ECR_REGISTRY}/streaming-admin:jenkins-${BUILD_NUMBER}
                '''
            }
        }

        stage('Build & Push Chat') {
            steps {
                sh '''
                    docker build \
                      -t ${ECR_REGISTRY}/streaming-chat:jenkins-${BUILD_NUMBER} \
                      -f ./backend/chatService/Dockerfile \
                      ./backend

                    docker push ${ECR_REGISTRY}/streaming-chat:jenkins-${BUILD_NUMBER}
                '''
            }
        }

        stage('Build & Push Frontend') {
            steps {
                sh '''
                    docker build \
                      -t ${ECR_REGISTRY}/streaming-frontend:jenkins-${BUILD_NUMBER} \
                      ./frontend

                    docker push ${ECR_REGISTRY}/streaming-frontend:jenkins-${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo 'All StreamingApp images built and pushed successfully.'
        }

        failure {
            echo 'StreamingApp CI pipeline failed.'
        }
    }
}