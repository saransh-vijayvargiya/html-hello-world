pipeline {
    agent any

    environment {
        IMAGE_NAME = "html-hello-world"
        IMAGE_TAG = "latest"
        DOCKER_HUB_USERNAME = "saranshvijayvargiya"  // ✅ Your Docker Hub username
        CONTAINER_NAME = "html-container"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Installing dependencies (if any)...'
                sh 'npm install || echo "No npm dependencies"'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests (if any)...'
                sh 'npm test || echo "No tests defined"'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    script {
                        sh "echo $PASSWORD | docker login -u $USERNAME --password-stdin"
                        sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} $DOCKER_HUB_USERNAME/${IMAGE_NAME}:${IMAGE_TAG}"
                        sh "docker push $DOCKER_HUB_USERNAME/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Pulling the latest image from Docker Hub..."
                    sh "docker pull ${DOCKER_HUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"

                    echo "Stopping and removing existing container if it exists..."
                    sh """
                        if [ \$(docker ps -aq -f name=^/${CONTAINER_NAME}\$) ]; then
                            docker stop ${CONTAINER_NAME} || true
                            docker rm ${CONTAINER_NAME} || true
                        fi
                    """

                    echo "Running new container on a random available port..."
                    sh "docker run -d -P --name ${CONTAINER_NAME} ${DOCKER_HUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"

                    echo "Mapped container ports:"
                    sh "docker port ${CONTAINER_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
    }
}
