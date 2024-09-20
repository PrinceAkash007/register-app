pipeline {
    agent {label 'slave'}
    
    tools {
        jdk 'java17'
        maven 'maven3'
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
    }
}
