pipeline {
    agent any

    tools {
        jdk 'JDK11'
        maven 'MAVEN3'
    }

    stages {

        stage('Clone Source Code') {
            steps {
                git url: 'https://github.com/Divyyaasy/Mvn_Web_App.git',
                    branch: 'main'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package'
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
