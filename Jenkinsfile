pipeline {
    agent any
    
    tools{
        jdk  'jdk17'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '15fb69c3-3460-4d51-bd07-2b0545fa5151', poll: false, url: 'https://github.com/jaiswaladi246/Shopping-Cart.git'
            }
        }      
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('Trivy') {
            steps {
                 sh "trivy fs ."
            }
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Shopping-Cart '''
               }
            }
        }       
        
        stage('Docker Build and Tag') {
            steps {
                script{
                    withDockerRegistory(credentialId: 'docker-cred', toolName: 'docker'){ 
                        sh "make image"
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                 sh "trivy image aaaa/python-webapp:latest"
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script{
                    withDockerRegistory(credentialId: 'docker-cred', toolName: 'docker'){ 
                        sh "make push"
                    }
                }
            }
        }

        stage('Deploy to Docker Container') {
            steps {
                script{
                    withDockerRegistory(credentialId: 'docker-cred', toolName: 'docker'){ 
                        sh "docker run -d -p 5000:5000 aaaa/python-webapp:latest"
                    }
                }
            }
        }
        
        
    }
}
