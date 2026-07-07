pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-app"
        IMAGE_TAG = "1.0"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                dir('app') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Docker Debug') {
            steps {
                sh '''
                    whoami
                    id
                    ls -l /var/run/docker.sock || true
                    docker version
                    docker ps
                '''
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
            echo '✅ Build Successful'
        }

        failure {
            echo '❌ Build Failed'
        }

        always {
            cleanWs()
        }
    }
}