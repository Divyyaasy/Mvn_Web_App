pipeline {
    agent any
    
    environment {
        APP_NAME = 'maven-webapp'
        DOCKER_REGISTRY = 'your-registry-url'  // e.g., Docker Hub or private registry
        DOCKER_IMAGE = "${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}"
        TOMCAT_URL = 'http://tomcat-server:8080'
        TOMCAT_CREDENTIALS_ID = 'tomcat-credentials'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your-repo/maven-webapp.git'
            }
        }
        
        stage('Build & Test') {
            steps {
                script {
                    sh 'mvn clean compile'
                    sh 'mvn test'
                }
            }
            
            post {
                success {
                    echo 'Tests passed successfully!'
                }
                failure {
                    echo 'Tests failed!'
                    error('Build failed due to test failures')
                }
            }
        }
        
        stage('Static Code Analysis') {
            steps {
                sh 'mvn checkstyle:check'
                sh 'mvn sonar:sonar -Dsonar.projectKey=maven-webapp'
            }
        }
        
        stage('Package WAR') {
            steps {
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-registry-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh "echo ${DOCKER_PASS} | docker login ${DOCKER_REGISTRY} -u ${DOCKER_USER} --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: TOMCAT_CREDENTIALS_ID,
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASS'
                    )]) {
                        // Using Tomcat Manager API
                        sh """
                            curl -v -u ${TOMCAT_USER}:${TOMCAT_PASS} \
                            "${TOMCAT_URL}/manager/text/undeploy?path=/" || true
                            
                            curl -v -u ${TOMCAT_USER}:${TOMCAT_PASS} \
                            --upload-file target/maven-webapp.war \
                            "${TOMCAT_URL}/manager/text/deploy?path=/&update=true"
                        """
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                script {
                    // Update Kubernetes deployment with new image
                    sh """
                        kubectl set image deployment/maven-webapp \
                        maven-webapp=${DOCKER_IMAGE} -n production
                    """
                }
            }
        }
        
        stage('Integration Test') {
            steps {
                script {
                    // Wait for application to be ready
                    sh 'sleep 30'
                    
                    // Run integration tests
                    sh '''
                        curl -f http://tomcat-server:8080/maven-webapp/ \
                        || exit 1
                    '''
                    
                    // Run automated UI tests if you have
                    sh 'mvn verify -Dit.test=IntegrationTest'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
            // Send notification
            slackSend(
                color: 'good',
                message: "Build ${BUILD_NUMBER} deployed successfully!"
            )
        }
        failure {
            echo 'Pipeline failed!'
            slackSend(
                color: 'danger',
                message: "Build ${BUILD_NUMBER} failed! Check Jenkins."
            )
        }
        always {
            // Cleanup
            sh 'docker system prune -f'
            
            // Generate test report
            junit 'target/surefire-reports/*.xml'
            
            // Archive test results
            archiveArtifacts artifacts: 'target/*.war, target/surefire-reports/*.xml'
        }
    }
}
