pipeline {
  agent any
  tools{
    nodejs 'nodejs 24-4-1'
  }
  stages{
    stage('version check') {
      steps {
       sh node -v
        sh npm -v
      }
    }
  }
