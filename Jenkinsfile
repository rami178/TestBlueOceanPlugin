pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
       checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rami178/Gestion-Personnel.git']])
      }
    }

    stage('Test') {
      steps {
        echo 'Test Completed'
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploy Completed'
      }
    }

  }
}