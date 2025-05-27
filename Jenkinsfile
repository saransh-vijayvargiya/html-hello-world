pipeline {
  agent any
  environment {
    DOCKERHUB_USER = 'saranshvijayvargiya'
    IMAGE_NAME = 'html-hello-world'
    IMAGE_TAG = "${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps {
        // This checks out latest code into workspace
        checkout scm
      }
    }
    stage('Build Image') {
      steps {
        script {
          sh """
            docker build --no-cache -t ${IMAGE_NAME}:${IMAGE_TAG} .
            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
          """
        }
      }
    }
    stage('Docker Login') {
      steps {
        script {
          sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
        }
      }
    }
    stage('Push Image') {
      steps {
        script {
          sh """
            docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
            docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
          """
        }
      }
    }
    stage('Deploy') {
      steps {
        script {
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
