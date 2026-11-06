pipeline {
    agent any

    environment {
        APP_NAME = 'docker-compose-app'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out application source code...'
                checkout scm
            }
        }

        stage('Check Docker Compose') {
            steps {
                echo 'Checking Docker Compose installation...'

                sh '''
                    docker compose version
                '''
            }
        }

        stage('Stop Old Application') {
            steps {
                echo 'Stopping old containers...'

                sh '''
                    docker compose down || true
                '''
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building Docker images...'

                sh '''
                    docker compose build
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Starting application containers...'

                sh '''
                    docker compose up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying application...'

                sh '''
                    sleep 5
                    docker compose ps
                    curl -f http://localhost:8082
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully using Docker Compose!'
        }

        failure {
            echo 'Deployment failed. Check the Jenkins Console Output.'
        }
    }
}
