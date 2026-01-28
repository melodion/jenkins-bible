pipeline {
    agent none   // ⬅️ PENTING!!!

    environment {
        APP_NAME = 'spring-apps'
        IMAGE_NAME = 'melodion/bible:latest'
    }

    stages {

        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Build Maven') {
            agent {
                docker {
                    image 'maven:3.9.9-eclipse-temurin-21'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            agent any
            steps {
                sh '''
                  docker build -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Run Container') {
            agent any
            steps {
                sh '''
                  docker stop ${APP_NAME} || true
                  docker rm ${APP_NAME} || true
                  docker run -d \
                    --name ${APP_NAME} \
                    -p 8081:8080 \
                    ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build & Deploy sukses'
        }
        failure {
            echo '❌ Build gagal'
        }
    }
}
