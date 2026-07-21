pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '<AWS_ACCOUNT_ID>'

        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"

        FRONTEND_REPO = "flight-frontend"
        BACKEND_REPO  = "flight-backend"

        CLUSTER_NAME = "demo-cluster"

        FRONTEND_IMAGE = "${ECR_REGISTRY}/${FRONTEND_REPO}:latest"
        BACKEND_IMAGE  = "${ECR_REGISTRY}/${BACKEND_REPO}:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                docker build -t flight-frontend ./Frontend
                '''
            }
        }

        stage('Build Backend') {
            steps {
                sh '''
                docker build -t flight-backend ./Backend
                '''
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login \
                --username AWS \
                --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag flight-frontend:latest $FRONTEND_IMAGE
                docker tag flight-backend:latest $BACKEND_IMAGE
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push $FRONTEND_IMAGE
                docker push $BACKEND_IMAGE
                '''
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region $AWS_REGION \
                  --name $CLUSTER_NAME
                '''
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                kubectl apply -f k8s/

                kubectl rollout restart deployment flight-frontend
                kubectl rollout restart deployment flight-backend
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get nodes
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout $ECR_REGISTRY || true'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
