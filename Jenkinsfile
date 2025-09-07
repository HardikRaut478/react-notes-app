pipeline {
    agent any

    parameters {
        choice(
            name: 'ACTION',
            choices: ['deploy', 'down'],
            description: 'Choose action: deploy to start or down to stop containers'
        )
    }

    environment {
        DOCKER_HUB_USER = 'hardik478'
        DOCKER_HUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                git branch: 'master', url: 'https://github.com/HardikRaut478/react-notes-app.git'
            }
        }

        stage('Docker Login') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Build & Push Images') {
            when {
                expression { params.ACTION == 'deploy' }
            }
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

        stage('Deploy or Down') {
            steps {
                script {
                    if (params.ACTION == 'deploy') {
                        sh """
                            docker compose -f docker-compose.yml pull
                            docker compose -f docker-compose.yml up -d
                        """
                    } else if (params.ACTION == 'down') {
                        sh """
                            docker compose -f docker-compose.yml down -v
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
