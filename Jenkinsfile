pipeline {
    agent {
        docker {
          image 'maven:3.9.9-eclipse-temurin-21'
          args '-v /root/.m2:/root/.m2'
        }
      }
    // Define Docker Hub credentials ID created in Jenkins
    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = 'your-dockerhub-username/your-app-name'
    }
    stages {

        stage('Checkout') {
          steps {
            checkout scm
          }
        }

        stage('Build Maven') {
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
                -p 8081:8081 \
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