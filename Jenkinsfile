pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'saranshvijayvargiya'
        IMAGE_NAME = 'html-hello-world'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_USER = credentials('dockerhub-creds-username')  // Adjust if needed
        DOCKER_PASS = credentials('dockerhub-creds-password')  // Adjust if needed
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out latest code...'
                checkout scm
                
                echo 'Listing workspace files:'
                sh 'ls -l'
                
                echo 'Showing index.html content:'
                sh 'cat index.html'
            }
        }

        stage('Build Image') {
            steps {
                echo "Building Docker image ${IMAGE_NAME}:${IMAGE_TAG} ..."
                sh """
                    docker build --no-cache -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Logging in to Docker Hub...'
                sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing Docker images to Docker Hub...'
                sh """
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Stopping and removing existing container (if any)...'
                sh 'docker rm -f html-hello-world || true'

                echo 'Pulling latest image...'
                sh "docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"

                echo 'Starting new container...'
                sh "docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }
    }
}
