pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'your-dockerhub-username'      // ✅ Replace this
        IMAGE_NAME = 'html-hello-world'
        IMAGE_TAG = "${BUILD_NUMBER}"                   // or use a timestamp for versioning
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
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
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
                # Remove existing container if running
                docker ps -q --filter "ancestor=${DOCKERHUB_USER}/${IMAGE_NAME}" | xargs -r docker rm -f

                # Pull the latest image and run
                docker pull ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                docker run -d -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                '''
            }
        }
    }
}
