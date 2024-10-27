pipeline {
    agent any
    tools{
        nodejs 'node21'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/PIYUSHKOKANE/3-Tier-Full-Stack.git'
            }
        }
        
        stage('Install Package Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Test Cases') {
            steps {
                sh "npm test"
            }
        }
        
        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o fs-report.html ."
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=CampGround -Dsonar.projectName=CampGround "
                }
            }
        }
        
        stage('Docker Build image & Tag image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t piyush797/camp:latest ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o image-report.html piyush797/camp:latest"
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push piyush797/camp:latest"
                    }
                }
            }
        }
        
        stage('Deploy to Dev'){
            steps {
                    script{
                        withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker run -d -p 3000:3000 piyush797/camp:latest"
                        }
                    }
                }
            
        }
    }
}
