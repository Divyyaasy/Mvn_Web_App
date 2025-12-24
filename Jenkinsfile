pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'MAVEN3'
    }

    stages {
        stage('Compile') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn package'
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
    }
}
