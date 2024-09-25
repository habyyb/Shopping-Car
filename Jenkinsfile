pipeline {
    agent any
    tools {
        jdk  'jdk11'
        maven  'maven3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/habyyb/Shopping-Car.git'
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-hub-credentials', toolName: 'Docker') {
                        
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart habib1234/shopping-cart:latest"
                        sh "docker push habib1234/shopping-cart:latest"
                    }
                }
            }
        }
        stage('Trigger CD Pipeline') {
            steps {
                build job: "CD_Pipeline" , wait: true
            }
        }
    }
}
        
    }
}
