pipeline {

    agent any


    tools {

        jdk 'jdk21'
        maven 'maven3'

    }


    environment {

        IMAGE_NAME = "devops-app"
        IMAGE_TAG = "1.0"

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

                    echo "Building Spring Boot Application"

                    mvn clean package -DskipTests


                    echo "Checking Jar"

                    ls -lh target/app-1.0.0.jar


                    '''

                }

            }

        }





        stage('SonarQube Analysis') {

            steps {

                dir('app') {


                    withSonarQubeEnv('sonar-server') {


                        sh '''

                        echo "Running SonarQube Scan"


                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-app \
                        -Dsonar.projectName=devops-app \
                        -Dsonar.sources=src \
                        -Dsonar.java.binaries=target/classes


                        '''


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





        stage('Docker Build') {

            steps {


                dir('app') {


                    sh '''

                    echo "Building Docker Image"


                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} .


                    docker images | grep ${IMAGE_NAME}


                    '''

                }

            }

        }





        stage('Trivy Security Scan') {

            steps {


                sh '''

                echo "Trivy Scan"


                trivy image \
                --no-progress \
                --severity HIGH,CRITICAL \
                --exit-code 1 \
                ${IMAGE_NAME}:${IMAGE_TAG}


                '''

            }

        }





        stage('Deploy Application') {


            steps {


                sh '''


                echo "Removing old container"


                docker rm -f springboot-app || true



                echo "Starting Application"



                docker run -d \
                --name springboot-app \
                --restart always \
                -p 8081:8080 \
                ${IMAGE_NAME}:${IMAGE_TAG}



                sleep 20



                docker ps | grep springboot-app



                echo "Application Started 🚀"



                '''

            }

        }


    }





    post {


        success {

            echo "CI/CD Pipeline Completed Successfully 🚀"

        }



        failure {

            echo "CI/CD Pipeline Failed ❌"

        }



        always {

            cleanWs()

        }


    }


}
