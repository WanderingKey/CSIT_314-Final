pipeline {
    agent any

    environment {
        NEXUS_URL  = 'http://nexus-service:8081'
        NEXUS_REPO = 'final-maven-repo'
        GROUP_ID   = 'com.example'
        ARTIFACT   = 'helloworld'
        VERSION    = '0.0.1-SNAPSHOT'
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
                    sh """
                    curl -v -u $NEXUS_USER:$NEXUS_PASS \
                    --upload-file target/${ARTIFACT}-${VERSION}.jar \
                    $NEXUS_URL/repository/$NEXUS_REPO/${GROUP_ID.replace('.', '/')}/${ARTIFACT}/${VERSION}/${ARTIFACT}-${VERSION}.jar
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deploy successful'
        }
        failure {
            echo 'Build or deploy failed'
        }
    }
}
