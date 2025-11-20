pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
    SONAR_AUTH_TOKEN = credentials('sonar-token')
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
         stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('sonar') {
            sh """
                mvn clean verify sonar:sonar \
                -Dsonar.projectKey=mi-proyecto \
                -Dsonar.projectName=mi-proyecto \
                -Dsonar.host.url=http://localhost:9000 \
                -Dsonar.login=${env.SONAR_AUTH_TOKEN}
            """
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
