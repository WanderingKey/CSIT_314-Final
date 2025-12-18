pipeline {
    agent any

    environment {
        MAVEN_OPTS = "-Dmaven.repo.local=.m2/repository"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                  chmod +x mvnw
                  ./mvnw clean package -DskipTests
                '''
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
                      mkdir -p $HOME/.m2

                      cat > $HOME/.m2/settings.xml <<EOF
<settings>
  <servers>
    <server>
      <id>nexus</id>
      <username>${NEXUS_USER}</username>
      <password>${NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
EOF

                      ./mvnw deploy -DskipTests
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
