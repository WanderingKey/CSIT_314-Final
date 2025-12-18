pipeline {
    agent any

    environment {
        NEXUS_REPO_URL = "http://nexus-service:8081/repository/final-maven-repo/"
        NEXUS_CREDENTIALS = "nexus-admin"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
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
                    ./mvnw deploy \
                      -DskipTests \
                      -Dnexus.username=$NEXUS_USER \
                      -Dnexus.password=$NEXUS_PASS
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deployment to Nexus completed successfully.'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
