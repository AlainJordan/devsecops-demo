pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/AlainJordan/devsecops-demo.git'
            }
        }

        stage('Build') {
            steps {
                dir('complete') {
                    sh 'mvn -B clean package'
                }
            }
        }

        stage('Tests') {
            steps {
               dir('complete') {
                    sh 'mvn test'
                }
            }
        }
    }

    post {
        success {
            echo 'Build completado exitosamente!'
        }
        failure {
            echo 'El build falló.'
        }
    }
}
