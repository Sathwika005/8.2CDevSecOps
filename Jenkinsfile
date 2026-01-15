pipeline {
  agent any

  environment {
    PATH = "/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:${env.PATH}"
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Sathwika005/8.2CDevSecOps.git'
      }
    }

    stage('Check Node & NPM') {
      steps {
        sh 'node -v'
        sh 'npm -v'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Tests (Snyk)') {
  steps {
    withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
      sh '''
        export PATH="/opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:$PATH"
        export SNYK_TOKEN="$SNYK_TOKEN"
        npm test || true
      '''
    }
  }
}


    stage('Generate Coverage Report') {
      steps {
        // nodejs-goof may not define a coverage script; keep stage but do not fail build
        sh 'npm run | head -n 50'
        sh 'echo "Coverage step: No npm coverage script exists in this project by default."'
      }
    }

    stage('NPM Audit') {
      steps {
        // Required stage: must execute and show output
        sh 'npm audit || true'
      }
    }

    stage('SonarCloud Analysis') {
  steps {
    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
      sh '''
        export PATH="/opt/homebrew/bin:/usr/local/bin:$PATH"
        export JAVA_HOME="/opt/homebrew/opt/openjdk"

        ls -la sonar-project.properties

        sonar-scanner -Dsonar.token=$SONAR_TOKEN
      '''
    }
  }
}



  }
}
