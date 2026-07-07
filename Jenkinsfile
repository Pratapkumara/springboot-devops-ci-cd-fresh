pipeline {

    agent any

    tools {
        maven 'maven3'
    }


    environment {

        IMAGE_NAME = "devops-app"
        IMAGE_TAG  = "1.2"

        SCANNER_HOME = tool 'sonar-scanner'
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

                    sh '''
                    mvn clean package -DskipTests
                    ls -lh target/
                    '''

                }
            }
        }



        stage('SonarQube Analysis') {

            steps {

                dir('app') {

                    withSonarQubeEnv('sonar-server') {

                        sh """

                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-app \
                        -Dsonar.projectName=devops-app \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes

                        """
                    }
                }
            }
        }



        stage('Quality Gate') {

            steps {

                timeout(time: 5, unit: 'MINUTES') {

                    waitForQualityGate abortPipeline: true

                }
            }
        }




        stage('Build Docker Image') {

            steps {

                dir('app') {

                    sh """

                    docker build --no-cache \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .

                    """

                }
            }
        }




        stage('Trivy Security Scan') {

            steps {

                sh '''

                trivy image \
                --no-progress \
                --exit-code 1 \
                --severity CRITICAL \
                ${IMAGE_NAME}:${IMAGE_TAG}

                '''

            }
        }




        stage('Deploy Container') {

            steps {

                sh '''

                docker rm -f springboot-app || true


                docker run -d \
                --name springboot-app \
                --restart always \
                -p 8081:8080 \
                ${IMAGE_NAME}:${IMAGE_TAG}


                docker ps | grep springboot-app

                '''

            }
        }


    }



    post {


        success {

            echo '✅ Build Successful - CI/CD Pipeline Completed'

        }


        failure {

            echo '❌ Pipeline Failed'

        }


        always {

            cleanWs()

        }

    }

}
