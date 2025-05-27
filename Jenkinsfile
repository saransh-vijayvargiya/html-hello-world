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
                # Stop and remove any container using port 80
                docker ps -q --filter "publish=80" | xargs -r docker rm -f || true

                # Pull the latest image
                docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest

                # Run new container on port 80 with fixed name
                docker run -d --name html-hello-world -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }
    }
}
