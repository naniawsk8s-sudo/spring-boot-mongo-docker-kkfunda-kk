
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
                ssh -o StrictHostKeyChecking=no ubuntu@13.127.136.136 << EOF

                docker pull newton9/mongospring:latest

                docker stop myspringcontainer || true
                docker rm myspringcontainer || true

                docker run -d \
                  --name myspringcontainer \
                  -p 8081:8080 \
                  --restart unless-stopped \
                  newton9/mongospring:latest

                docker ps

				EOF
					'''
				}
			}
		}
    }
}
