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
                        --scan .
                    ''',
                    odcInstallation: 'dependency-check'
                }
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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
