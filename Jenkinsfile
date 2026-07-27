//testing
//testing
pipeline {
    agent any

    tools {
        maven 'maven-3.9.10' // This should match the Maven name in Jenkins Global Tool Configuration
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
		
		stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=spring-boot-mongo \
                        -Dsonar.projectName='Spring Boot Mongo Project' \
                    """
                }
            }
        }
    }
}
