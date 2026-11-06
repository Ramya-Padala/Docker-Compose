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

stage('Install Docker Compose') {
    steps {
        echo 'Installing Docker Compose automatically if missing...'

        sh '''
            set -e

            if ! docker compose version >/dev/null 2>&1; then

                echo "Adding Docker official repository..."

                sudo apt-get update
                sudo apt-get install -y ca-certificates curl

                sudo install -m 0755 -d /etc/apt/keyrings

                sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
                    -o /etc/apt/keyrings/docker.asc

                sudo chmod a+r /etc/apt/keyrings/docker.asc

                . /etc/os-release

                echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu ${UBUNTU_CODENAME:-$VERSION_CODENAME} stable" \
                    | sudo tee /etc/apt/sources.list.d/docker.list

                sudo apt-get update

                sudo apt-get install -y docker-compose-plugin

            else
                echo "Docker Compose is already installed."
            fi

            docker compose version
        '''
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
