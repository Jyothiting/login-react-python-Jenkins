pipeline {

    agent any
 
    environment {

        APP_DIR = '/home/ubuntu/login-react-python'

    }
 
    stages {
 
        stage('Update Code') {

            steps {

                sh '''

                    cd ${APP_DIR}

                    git fetch origin

                    git reset --hard origin/main

                '''

            }

        }
 
        stage('Build') {

            steps {

                sh '''

                    cd ${APP_DIR}

                    docker compose build

                '''

            }

        }
 
        stage('Deploy') {

            steps {

                sh '''

                    cd ${APP_DIR}

                    docker compose up -d

                '''

            }

        }
 
        stage('Verify') {

            steps {

                sh '''

                    cd ${APP_DIR}

                    docker compose ps

                '''

            }

        }

    }
 
    post {

        success {

            echo 'Application deployed successfully!'

        }
 
        failure {

            echo 'Application deployment failed!'

        }

    }

}
 
