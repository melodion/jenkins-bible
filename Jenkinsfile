pipeline {
    agent any
    // Define Docker Hub credentials ID created in Jenkins
    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = 'your-dockerhub-username/your-app-name'
    }
    stages {
        stage('Build Jar') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image with a specific tag (e.g., using a build number)
                    sh "docker build -t ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ."
                    sh "docker tag ${env.IMAGE_NAME}:${env.BUILD_NUMBER} ${env.IMAGE_NAME}:latest"
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKERHUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USER')]) {
                    sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD}"
                    sh "docker push ${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "docker push ${env.IMAGE_NAME}:latest"
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                // Stop and remove the old container if it exists, then run the new one
                sh 'docker stop spring-app-container || true'
                sh 'docker rm spring-app-container || true'
                sh "docker run -d --name spring-app-container -p 8088:8088 ${env.IMAGE_NAME}:latest"
            }
        }
    }
}
