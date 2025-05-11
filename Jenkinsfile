pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-2'
        IMAGE_NAME = 'simple-java-app'
        ECR_REPO = '123456789012.dkr.ecr.us-west-2.amazonaws.com/simple-java-app'
        MANIFEST_REPO = 'git@github.com:my-repo/manifest.git'
    }

    stages {

        stages {

            stage("Checkout SCM"){
            steps{
                script{
                    SCM = checkout scm
                }
            }
            }

        stage('Checkout App Repository'){
            steps{
                script{
                println "Checkout App Repository"
                }
                dir("../pr_attestation"){
                checkout ( [$class: 'GitSCM',
                    branches: [[name: "main" ]],
                    userRemoteConfigs: [[
                    credentialsId: 'jenkins',
                    url: 'git@github.com:prophius/pr_attestation.git'
                    ]]] ) 
                }
            }
        }

        stage('Build and Dockerize') {
            steps {
                script {
                    // Build the jar file
                    // sh 'mvn clean package -DskipTests'

                    // Build Docker image
                    sh """
                    docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${ECR_REPO}:${BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    // Login to ECR
                    sh """
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REPO}
                    docker push ${ECR_REPO}:${BUILD_NUMBER}
                    """
                }
            }
        }

        stage('Update Manifest') {
            steps {
                script {
                    // Clone manifest repo
                    sh """
                    git clone ${MANIFEST_REPO} manifest-repo
                    cd manifest-repo

                    # Update the image tag
                    sed -i 's|image: .*$|image: ${ECR_REPO}:${BUILD_NUMBER}|' ${MANIFEST_FILE}

                    # Commit and push the changes
                    git config user.name "Jenkins"
                    git config user.email "jenkins@example.com"
                    git add ${MANIFEST_FILE}
                    git commit -m "Update image tag to ${BUILD_NUMBER}"
                    git push origin main
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Build, Dockerize, and Manifest Update Successful!'
        }
        failure {
            echo 'Build failed. Please check the logs.'
        }
    }
}
