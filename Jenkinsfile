pipeline {
    agent any

    environment {
        registry = "503499294473.dkr.ecr.us-east-1.amazonaws.com/simple-java-app"
    }
    
    tools {
        maven 'Maven3'  
    }
    
    stages {
        
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kingsleychino/simple-java-app-jenkins.git'
            }
        }

        stage('Build Java App') {
            steps {
                // Assuming a Maven project, adjust for Gradle or other build tools
                sh 'mvn clean package -DskipTests' 
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }

}
