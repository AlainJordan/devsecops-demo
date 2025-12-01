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
                    dependencyCheckPublisher pattern: 'complete/dependency-check-report.xml'
            
                    publishHTML([
                        reportDir: 'complete',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'OWASP Dependency Check Report',
                        keepAll: true,
                        alwaysLinkToLastBuild: true,
                        allowMissing: true
                    ])
                }
            }
        }


        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        docker build -t alainjordan07/devsecops-demo:latest .
                    '''
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    docker push alainjordan07/devsecops-demo:latest
                '''
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
