pipeline {
    agent { label 'slave' }
    
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage('Cleanup workspace') {
            steps {
                // Cleanup workspace before starting build
                cleanWs()
            }
        }
        
        stage('Checkout Git') {
            steps {
                // Checkout Code from GitHub
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/PrinceAkash007/register-app.git'
            }
        }
        
        stage('Build application') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Test application') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('Sonar Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false  // Removed credentialsId here as it's not needed
                }
            }
        }
        
        stage('Build & Push docker image') {
            steps {
                sh """
                # Build Docker image
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                
                # Tag image with 'latest' tag
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                
                # Push both the versioned tag and the 'latest' tag to DockerHub
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${IMAGE_NAME}:latest
                """
            }
        }
    }
}
