pipeline {
    agent any

    stages {

        stage('Build Frontend') {
            steps {
                sh 'docker build -t flight-frontend ./frontend'
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker build -t flight-backend ./backend'
            }
        }

        stage('Stop Old Containers') {
            steps {
                sh 'docker rm -f flight-frontend || true'
                sh 'docker rm -f flight-backend || true'
            }
        }

        stage('Run Backend') {
            steps {
                sh 'docker run -d --name flight-backend -p 8080:8080 flight-backend'
            }
        }

        stage('Run Frontend') {
            steps {
                sh 'docker run -d --name flight-frontend -p 5000:5000 flight-frontend'
            }
        }

        stage('Verify') {
            steps {
                sh 'docker ps'
            }
        }
    }
}
