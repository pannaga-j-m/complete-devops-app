pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "pannagajm2004"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/flight-frontend"
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/flight-backend"
    }

    stages {

        stage('Build Frontend') {
            steps {
                sh 'docker build -t flight-frontend ./Frontend'
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker build -t flight-backend ./Backend'
            }
        }

        stage('Stop & Remove Old Containers') {
            steps {
                sh '''
                docker rm -f flight-frontend || true
                docker rm -f flight-backend || true
                '''
            }
        }

        stage('Run Backend') {
            steps {
                sh '''
                docker run -d \
                  --name flight-backend \
                  -p 3000:3000 \
                  flight-backend
                '''
            }
        }

        stage('Run Frontend') {
            steps {
                sh '''
                docker run -d \
                  --name flight-frontend \
                  -p 5000:5000 \
                  flight-frontend
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker ps'
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag flight-frontend ${FRONTEND_IMAGE}:latest
                docker tag flight-backend ${BACKEND_IMAGE}:latest
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push ${FRONTEND_IMAGE}:latest
                docker push ${BACKEND_IMAGE}:latest
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }

        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
