pipeline {
    agent any

    environment {
        IMAGE_NAME = 'html-hello-world'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/saransh-vijayvargiya/html-hello-world.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                # Stop and remove any existing container using the same image
                docker ps -q --filter ancestor=${IMAGE_NAME} | xargs -r docker rm -f

                # Run the container on port 80
                docker run -d -p 80:80 ${IMAGE_NAME}
                '''
            }
        }
    }
}
