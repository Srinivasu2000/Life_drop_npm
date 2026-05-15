pipeline {

    agent any

    environment {
        DOCKERHUB_USERNAME = "srinivasu56"

        BACKEND_IMAGE = "${DOCKERHUB_USERNAME}/lifedrop-backend:latest"

        FRONTEND_IMAGE = "${DOCKERHUB_USERNAME}/lifedrop-frontend:latest"
    }

    tools {
        jdk 'jdk21'
    }

    stages {

        stage('Clone Repository') {
            steps {

                git branch: 'main',
                url: 'https://github.com/Srinivasu2000/Life_drop_npm.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {

                sh '''
                docker build -t $BACKEND_IMAGE ./backend
                '''
            }
        }

        stage('Push Backend Docker Image') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $BACKEND_IMAGE
                    '''
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {

                sh '''
                docker build -t $FRONTEND_IMAGE ./frontend
                '''
            }
        }

        stage('Push Frontend Docker Image') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $FRONTEND_IMAGE
                    '''
                }
            }
        }

        stage('Deploy Containers') {
            steps {

                sh '''
                cd /var/lib/jenkins/workspace/Life_drop

                docker-compose down || true

                docker rm -f lifedrop-nginx lifedrop-frontend lifedrop-backend lifedrop-mongo || true

                docker-compose pull

                docker-compose up -d

                docker image prune -f
                '''
            }
        }

        stage('Verify Running Containers') {
            steps {

                sh '''
                docker ps
                '''
            }
        }
    }

    post {

        success {

            echo '==================================='
            echo 'Pipeline Executed Successfully!'
            echo 'Application Deployed Successfully!'
            echo '==================================='
        }

        failure {

            echo '==================================='
            echo 'Pipeline Failed!'
            echo 'Check Jenkins Console Output'
            echo '==================================='
        }
    }
}
