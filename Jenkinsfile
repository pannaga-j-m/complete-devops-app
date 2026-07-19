pipeline {
    agent any

    stages {

        stage('Verify Tools') {
            steps {
                sh 'git --version'
                sh 'docker --version'
                sh 'docker compose --version'
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images...'
                sh 'docker compose build'
            }
        }

        stage('Stop Existing Containers') {
            steps {
                echo 'Stopping old containers...'
                sh 'docker compose down || true'
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Starting containers...'
                sh 'docker compose up -d'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Running containers:'
                sh 'docker ps'
            }
        }
    }

    post {
        success {
            echo '======================================='
            echo 'Application deployed successfully!'
            echo '======================================='
        }

        failure {
            echo '======================================='
            echo 'Deployment failed!'
            echo '======================================='
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
