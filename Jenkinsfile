pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'saranshvijayvargiya'  
        IMAGE_NAME = 'html-hello-world'         
        IMAGE_TAG = "${BUILD_NUMBER}"             
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/saransh-vijayvargiya/html-hello-world.git'
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
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
                # Force remove container named html-hello-world (ignore error if none)
                docker rm -f html-hello-world || true

                # Remove any container exposing port 80 (ignore errors)
                docker ps -q --filter "publish=80" | xargs -r docker rm -f || true

                # Pull the latest image
                docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest

                # Run container with fixed name and expose port 80
                docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }
    }
}
