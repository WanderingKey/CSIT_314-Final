pipeline {
  agent any

  environment {
    // Change these if your services are exposed differently.
    NEXUS_URL  = 'http://nexus-service:8081'
    NEXUS_REPO = 'final-maven-repo'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'chmod +x mvnw || true'
        sh './mvnw -B -DskipTests=false clean test'
      }
    }

    stage('Package') {
      steps {
        sh './mvnw -B -DskipTests package'
      }
    }

    stage('Deploy to Nexus') {
      steps {
        // Uses Jenkins-managed credentials: create a Username/Password credential
        // with ID: nexus-creds (username = Nexus user, password = Nexus password)
        withCredentials([usernamePassword(
          credentialsId: 'nexus-creds',
          usernameVariable: 'NEXUS_USER',
          passwordVariable: 'NEXUS_PASS'
        )]) {
          sh '''
            mkdir -p $HOME/.m2

            cat > $HOME/.m2/settings.xml <<EOF
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>nexus</id>
      <username>${NEXUS_USER}</username>
      <password>${NEXUS_PASS}</password>
    </server>
  </servers>
</settings>
EOF

            ./mvnw -B -DskipTests deploy
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'target/*.jar', fingerprint: true, onlyIfSuccessful: false
    }
    success {
      echo 'Pipeline finished successfully.'
    }
    failure {
      echo 'Build or deploy failed. Check Console Output.'
    }
  }
}
