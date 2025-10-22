pipeline {
    agent any 
   
    environment {
        IMAGE_NAME = 'nodejs'
        DOCKERHUB_USER = 'vardhan1228'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the Docker image...'
                sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest .'
            }
        }
        stage('Push to DockerHub') {
            steps {
                echo 'Pushing Docker image to DockerHub...'
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKERHUB_TOKEN')]) {
                    sh '''
                    echo "$DOCKERHUB_TOKEN" | docker login -u "$DOCKERHUB_USER" --password-stdin
                    docker push $DOCKERHUB_USER/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the app...'
                // Example: deploy using Docker Compose or Kubernetes
                sh 'docker run -d -p 3000:3000 $DOCKERHUB_USER/$IMAGE_NAME:latest'
            }
        }
    }

}
