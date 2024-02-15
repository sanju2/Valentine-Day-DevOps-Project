pipeline {
    agent any
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Get Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaiswaladi246/Valentine-Day-DevOps-Project.git'
                }
            }
    
        stage('Trivy FileSystem Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
    
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine"
                }
            }
        }
    
        stage('Build & Tag Docker Image') {
            steps {
                script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t lasanthasanjeewa/valentine:v1 ."
                   }
                }
            }
        }
    
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format json -o trivy-image-report.json lasanthasanjeewa/valentine:v1"
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker push lasanthasanjeewa/valentine:v1 "
                   }
                }
            }
        }
        
        stage('Deploy To container') {
            steps {
                sh "docker run -d -p 8081:80 lasanthasanjeewa/valentine:v1"
            }
        }
    
    }
}