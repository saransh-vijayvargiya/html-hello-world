pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'saranshvijayvargiya'  // Replace with your Docker Hub username
        IMAGE_NAME = 'html-hello-world'
        IMAGE_TAG = "${BUILD_NUMBER}"           // Jenkins build number as tag/version
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/saransh-vijayvargiya/html-hello-world.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                # Stop and remove any running container named html-hello-world
                docker rm -f html-hello-world || true

                # Pull the latest image
                docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest

                # Run container with fixed name and map port 80
                docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }
    }
}
