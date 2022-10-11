pipeline {
    agent any
    environment {
        ECR_REGISTRY = "763265662616.dkr.ecr.us-east-1.amazonaws.com"
        APP_REPO_NAME= "clarusway/to-do-app"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build --force-rm -t "$ECR_REGISTRY/$APP_REPO_NAME:latest" .'
                sh 'docker image ls'
            }
        }
        stage('Push Image to ECR Repo') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                sh 'docker push "$ECR_REGISTRY/$APP_REPO_NAME:latest"'
            }
        
        }
        stage('Deploy') {
            steps {
                sh 'aws ecs update-service --cluster to-do-app --desired-count 1 --service to-do-app-service --task-definition to-do-app --force-new-deployment'
            }
        }
    }
    post {
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
    }
}