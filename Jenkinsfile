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


                    echo "Checking Artifact"

                    ls -lh target/*.jar


                    '''

                }

            }

        }




        stage('SonarQube Analysis') {

            steps {

                dir('app') {


                    withSonarQubeEnv('Sonar-Qube') {


                        sh '''

                        echo "Starting SonarQube Scan"


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

                echo "Running Trivy Scan"


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


                echo "Stopping Old Container"


                docker rm -f springboot-app || true



                echo "Starting New Container"



                docker run -d \
                --name springboot-app \
                --restart unless-stopped \
                -p 8081:8080 \
                ${IMAGE_NAME}:${IMAGE_TAG}



                echo "Waiting For Application"


                sleep 20



                echo "Container Status"


                docker ps | grep springboot-app



                echo "Application Logs"


                docker logs --tail 50 springboot-app



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
