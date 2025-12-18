pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://nexus-service:8081'
        NEXUS_REPO = 'final-maven-repo'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/WanderingKey/CSIT_314-Final.git'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-admin',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                    ./mvnw deploy -DskipTests=true
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deploy completed successfully'
        }
        failure {
            echo 'Build or deploy failed'
        }
    }
}
