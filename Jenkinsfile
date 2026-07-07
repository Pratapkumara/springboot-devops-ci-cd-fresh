pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-app"
        IMAGE_TAG = "1.0"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                dir('app') {
                    sh 'chmod +x mvnw || true'
                    sh './mvnw clean package -DskipTests || mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker rm -f springboot-app || true
                docker run -d --name springboot-app -p 8081:8080 ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo 'Build Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}