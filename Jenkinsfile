pipeline {
    agent any
    environment {
        IMAGE_NAME = 'html-hello-world'
        DOCKERHUB_USER = 'saranshvijayvargiya'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/saransh-vijayvargiya/html-hello-world.git'
            }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin'
                    sh "docker tag $IMAGE_NAME $DOCKERHUB_USER/$IMAGE_NAME:latest"
                    sh "docker push $DOCKERHUB_USER/$IMAGE_NAME:latest"
                }
            }
        }
        stage('Deploy') {
    steps {
        script {
            echo "Pulling latest image from Docker Hub..."
            sh "docker pull ${DOCKER_HUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"

            echo "Stopping any existing container named ${CONTAINER_NAME}..."
            sh """
                if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                fi
            """

            echo "Running new container on port 80 with the latest image..."
            sh "docker run -d -p 80:80 --name ${CONTAINER_NAME} ${DOCKER_HUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        }
    }
}

    post {
        success {
            echo 'Static HTML deployed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
