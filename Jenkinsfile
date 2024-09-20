pipeline {
    agent {label 'slave'}
    
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    environment {
      APP_NAME = "register-app-pipeline"
      RELEASE = "1.0.0"
      DOCKER_USER = "akash2147"
      DOCKER_PASS = "docker-credentials"
      IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
      IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages{
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
            steps{
                sh "mvn clean package"
            }    
        }
        stage('Test application') {
            steps{
                sh "mvn test"
            }
        }
        stage('Sonar Analysis'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("Quality Gate"){
                steps{
                    script{
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                    }
                }
            }
        stage('Build & Push docker image'){
            steps {
                script{
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image= docker.build "${IMAGE_NAME}"
                    }
                    docker.withRegistry('',DOCKER_PASS){
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
        }
    }
