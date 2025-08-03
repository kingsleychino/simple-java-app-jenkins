def tomcatServerUrl = "http://172.31.14.95"
pipeline {
    agent {
        node {
            label 'jenkins-slave'
        }
    }

    stages {
        stage('Build') {
            steps {
                // RUN THE MAVEN BUILD
                sh '"mvn" -Dmaven.test.failure.ignore clean install'
            }
        }

        stage('Deploy') {
            steps {
                // DEPLOY WAR ON TOMCAT SERVER
                deploy adapters: [tomcat8(url: "${tomcatServerUrl}",
                    credentialsId: 'tomcat-credentials')],
                war: 'target/*.war',
                contextPath: 'pipeline-app'
            }
        }
    }
}