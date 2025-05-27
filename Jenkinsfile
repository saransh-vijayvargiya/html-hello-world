pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'your-dockerhub-username'       // Change this
        IMAGE_NAME = 'html-hello-world'                   // Change if needed
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds'        // Your Jenkins Docker Hub creds ID
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your GitHub repo
                git 'https://github.com/saransh-vijayvargiya/html-hello-world.git'
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}", ".")
                    docker.tag("${IMAGE_NAME}:${IMAGE_TAG}", "${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
                    docker.tag("${IMAGE_NAME}:${IMAGE_TAG}", "${DOCKERHUB_USER}/${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', "${DOCKERHUB_CREDENTIALS}") {
                        docker.image("${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}").push()
                        docker.image("${DOCKERHUB_USER}/${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Remove running container if exists
                    sh """
                    docker rm -f html-hello-world || true
                    docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
}
