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
                dir('complete') { 
                    withSonarQubeEnv('sonar') {
                        sh """
                            mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=mi-proyecto \
                            -Dsonar.projectName=mi-proyecto
                        """
                    }
                }
                
            }
        }


       stage('Dependency Check') {
            steps {
                dir('complete') {
                    dependencyCheck additionalArguments: '''
                        --format XML \
                        --format HTML \
                        --scan . \
                        --failOnCVSS 7
                    ''',
                    odcInstallation: 'dependency-check'
                }
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                    publishHTML(target: [
                        reportDir: 'complete',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'Dependency Check Report'
                    ])
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
