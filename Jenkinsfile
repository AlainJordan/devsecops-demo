pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'TU_REPO_AQUI'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Tests') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
