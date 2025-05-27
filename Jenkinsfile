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
                    
                    sh "docker run -d -p 80:80 --name html-container $DOCKERHUB_USER/$IMAGE_NAME:latest"
                }
            }
        }
    }

   
}
