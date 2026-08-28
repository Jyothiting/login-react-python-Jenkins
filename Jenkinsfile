pipeline {

    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        DEPLOY_USER = "ubuntu"
        DEPLOY_HOST = "3.110.30.13"
        APP_DIR = "/home/ubuntu/login-react-python-Jenkins"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Verify Jenkins') {
            steps {
                sh '''
                    echo "Jenkins server:"
                    hostname

                    echo "Git:"
                    git --version

                    echo "SSH:"
                    ssh -V
                '''
            }
        }

        stage('Test SSH Connection') {
            steps {
                sshagent(credentials: ['login-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${DEPLOY_USER}@${DEPLOY_HOST} \
                            "hostname && docker --version && docker compose version"
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {

                sshagent(credentials: ['login-server-ssh']) {

                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${DEPLOY_USER}@${DEPLOY_HOST} << EOF

                        set -e

                        echo "======================================"
                        echo "Starting deployment"
                        echo "======================================"

                        cd ${APP_DIR}

                        echo "Current directory:"
                        pwd

                        echo "Pulling latest code..."

                        git fetch origin
                        git reset --hard origin/main

                        echo "Docker Compose version:"
                        docker compose version

                        echo "Stopping old containers..."
                        docker compose down

                        echo "Building Docker images..."
                        docker compose build

                        echo "Starting application..."
                        docker compose up -d

                        echo "Waiting for containers..."
                        sleep 10

                        echo "======================================"
                        echo "Docker Compose Status"
                        echo "======================================"

                        docker compose ps

                        echo "======================================"
                        echo "Backend health check"
                        echo "======================================"

                        curl --fail \
                            --silent \
                            --show-error \
                            http://localhost:8081/docs > /dev/null

                        echo "Backend Swagger is working."

                        echo "======================================"
                        echo "Frontend health check"
                        echo "======================================"

                        curl --fail \
                            --silent \
                            --show-error \
                            http://localhost:8080 > /dev/null

                        echo "Frontend is working."

                        echo "======================================"
                        echo "DEPLOYMENT SUCCESSFUL"
                        echo "======================================"

                        EOF
                    '''
                }
            }
        }
    }

    post {

        success {
            echo 'Application deployed successfully.'
        }

        failure {
            echo 'Application deployment failed.'
        }
    }
}
