pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/siddu988/Jenkinsandjava.git'
        AWS_REGION = 'us-east-1'
        ECR_REPO_NAME = 'project-test1'
        ECR_PUBLIC_REPO_URI = '333095588216.dkr.ecr.us-east-1.amazonaws.com/project-test1'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '333095588216'
        IMAGE_URI = "${ECR_REPO_URI}:${IMAGE_TAG}"
        
    }


        stage('Clone Repository') {
            steps {
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh '''
                        echo "Building Java application..."
                        mvn clean -B -Denforcer.skip=true package
                    '''
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                script {
                    sh '''
                        echo "Logging into AWS ECR..."
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 333095588216.dkr.ecr.us-east-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        echo "Building Docker image..."
                        docker build -t ${IMAGE_URI} .
                    '''
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                script {
                    sh '''
                        echo "Pushing Docker image to ECR..."
                        docker push ${IMAGE_URI}
                    '''
                }
            }
        }

      
    }
    
    post {
        success {
            echo "Docker image pushed to ECR successfully and deployed."
        }
        failure {
            echo "Pipeline failed."
        }
    }
}
