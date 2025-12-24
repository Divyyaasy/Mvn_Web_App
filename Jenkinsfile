pipeline {
    agent any

    environment {
        // Set your JDK path
        JAVA_HOME = '/usr/lib/jvm/java-17-openjdk'   // <-- replace with your JDK path
        PATH = "${JAVA_HOME}/bin:${env.PATH}"

        // Set your Maven path
        MAVEN_HOME = '/opt/maven/apache-maven-3.9.9' // <-- replace with your Maven path
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from GitHub
                git 'https://github.com/Divyyaasy/Mvn_Web_App.git'
            }
        }

        stage('Build') {
            steps {
                // Run Maven build
                sh 'mvn clean install'
            }
        }

        stage('Test') {
            steps {
                // Run tests if any
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                // Package or deploy if needed
                sh 'mvn package'
            }
        }
    }
}
