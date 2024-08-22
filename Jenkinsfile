pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'Sonar_Scanner'
    }
    stages {
        stage("Code Checkout") {
            steps {
                git url: "https://github.com/Sachin-panigrahi/DevSecOps_implementation", branch: "main"
            }
        }
        stage("Static Code Analysis") {
            steps {
                withSonarQubeEnv('Sonar_Scanner') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=DevSecOps_Beginning \
                    -Dsonar.projectKey=DevSecOps-CICD'''
                }
            }
        }
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'Dependency_Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker Image Building") {
            steps{
                sh "docker build -t go-devops-chatbot:latest ."
            }
        }
        stage("Image Scanning by Trivy") {
            steps{
            sh "trivy image go-devops-chatbot:latest --format json --output trivy-report.json"

        }
        }
        stage("Docker Push Image to DockerHub") {
            steps{
                script{
                    withDockerRegistry(credentialsId: 'Docker_Cred', toolName: 'Docker') {
                     sh "docker tag go-devops-chatbot:latest sachinpanigrahi/go-devops-chatbot:latest"
                     sh "docker push sachinpanigrahi/go-devops-chatbot:latest"
                   }
                }
            }
        }
    }
}
