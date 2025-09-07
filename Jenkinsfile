pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'hardik478'
        DOCKER_HUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/HardikRaut478/react-notes-app.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Build & Push Images') {
            steps {
                script {
                    sh """
                        docker build -t ${DOCKER_HUB_USER}/notes-backend:latest ./notes-backend
                        docker push ${DOCKER_HUB_USER}/notes-backend:latest

                        docker build -t ${DOCKER_HUB_USER}/notes-frontend:latest ./notes-frontend
                        docker push ${DOCKER_HUB_USER}/notes-frontend:latest
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    docker compose -f docker-compose.yml pull
                    docker compose -f docker-compose.yml up -d
                """
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
