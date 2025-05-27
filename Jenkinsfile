pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'saranshvijayvargiya'
        IMAGE_NAME = 'html-hello-world'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_USER = 'saranshvijayvargiya'  // Docker Hub username
        DOCKER_PASS = credentials('dockerhub-password') // Jenkins credential ID
    }

    stages {
        stage('Checkout') {
            steps {
                // Simple git checkout, assumes public repo
                git url: 'https://github.com/saransh-vijayvargiya/html-hello-world.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh """
                    docker build --no-cache -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Docker Login') {
            steps {
                sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
            }
        }

        stage('Push') {
            steps {
                sh """
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    docker rm -f html-hello-world || true
                    docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }
    }
}
