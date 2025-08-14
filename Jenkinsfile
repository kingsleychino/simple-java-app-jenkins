pipeline {
    agent any

    environment {
        // Replace with your ECR repository URI
        ECR_REPOSITORY_URI = '503499294473.dkr.ecr.us-east-1.amazonaws.com/simple-java-app
        // Replace with your ECR region
        AWS_REGION = 'us-east-1'
        // Replace with your AWS credentials ID in Jenkins
        AWS_CREDENTIALS_ID = 'aws-creds'
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
                sh 'mvn clean install'
            }
        }

        /*
        stage('Build Java App') {
            steps {
                // Assuming a Maven project, adjust for Gradle or other build tools
                sh 'mvn clean package -DskipTests' 
            }
        } */

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${ECR_REPOSITORY_URI}:${env.BUILD_NUMBER}")
                }
            }
        }

        sta

        stage('Push to ECR') {
            steps {
                script {
                    withAWS(credentials: AWS_CREDENTIALS_ID, region: AWS_REGION) {
                        sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPOSITORY_URI}"
                        sh "docker push ${ECR_REPOSITORY_URI}:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
        
        /*
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        } */
        
    }
    
    /*
    post {
        success {
            echo '✅ Build completed successfully!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
    */

    post {
        always {
            // Clean up Docker images to save space (optional)
            sh "docker rmi ${ECR_REPOSITORY_URI}:${env.BUILD_NUMBER} || true"
        }
    }
}
