
pipeline {
    agent any

    tools {
        maven 'maven-3.9.10'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/naniawsk8s-sudo/spring-boot-mongo-docker-kkfunda-kk.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''
                        mvn clean verify \
                        org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                        -Dsonar.projectKey=spring-boot-mongo \
                        -Dsonar.projectName="Spring Boot Mongo Project"
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh 'docker build -t newton9/mongospring:latest .'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        sh 'docker push newton9/mongospring:latest'
                    }
                }
            }
        }
		stage('Deploy to CD Server') {
    steps {
        sshagent(['ssh']) {
            sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@13.127.136.136 "
                    docker pull newton9/mongospring:latest &&

                    docker network create jionetwork || true &&

                    docker stop springapp mongo || true &&
                    docker rm springapp mongo || true &&

                    docker run -d \
                      --name mongo \
                      --network jionetwork \
                      -e MONGO_INITDB_ROOT_USERNAME=devdb \
                      -e MONGO_INITDB_ROOT_PASSWORD=dev@123 \
                      mongo:8.0.9-noble &&

                    sleep 15 &&

                    docker run -d \
                      --name springapp \
                      --network jionetwork \
                      -p 8090:8080 \
                      -e MONGO_DB_HOSTNAME=mongo \
                      -e MONGO_DB_USERNAME=devdb \
                      -e MONGO_DB_PASSWORD=dev@123 \
                      --restart unless-stopped \
                      newton9/mongospring:latest &&

                    docker ps
                "
            '''
       			 }
    		}
		}		
    }
}
