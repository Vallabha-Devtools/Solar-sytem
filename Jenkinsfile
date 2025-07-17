pipeline {
  agent any

  tools {
    nodejs 'NodeJS 24.4.1' // Make sure this matches your NodeJS tool name in Jenkins
  }

  stages {
    stage('Check Node & npm Versions') {
      steps {
        sh 'node -v && npm -v'
      }
    }
  }
}

