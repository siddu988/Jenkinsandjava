pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/siddu988/Jenkinsandjava.git'
        AWS_REGION = 'us-west-2'
        ECR_REPO_NAME = 'project-test1'
        ECR_REPO_URI = '919333998358.dkr.ecr.us-west-2.amazonaws.com/project-test1'
        IMAGE_TAG = 'latest'
        IMAGE_URI = "${ECR_REPO_URI}:${IMAGE_TAG}"
    }

    stages {

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
                        aws ecr get-login-password --region us-west-2 | \
                        docker login --username AWS --password-stdin 919333998358.dkr.ecr.us-west-2.amazonaws.com
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
                        echo "Pushing Docker image..."
                        docker push ${IMAGE_URI}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                   kubectl apply -f deploymentjava.yaml
                   kubectl apply -f servicelb.yaml
                   kubectl rollout restart deployment/java-app
               '''
           }
        }
    }

    post {
        success {
            echo "Docker image pushed to ECR successfully"
        }
        failure {
            echo "Pipeline failed"
        }
       
        
    }
}
