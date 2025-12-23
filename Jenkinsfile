pipeline {
    agent any

    tools {
        jdk 'JDK11'       // Name of JDK configured in Jenkins
        maven 'MAVEN3'    // Name of Maven configured in Jenkins
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Divyyaasy/Mvn_Web_App.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

    }

    post {
        success {
            echo '✅ Maven Build Successful! WAR generated in target folder.'
        }
        failure {
            echo '❌ Build Failed! Check logs.'
        }
    }
}
