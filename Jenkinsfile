pipeline {
  agent any
  stages {
    stage('Checkout from GitHub') {
      steps {
        checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rami178/Gestion-Personnel.git']])
      }
    }

    stage('Build') {
      steps {
        checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rami178/Gestion-Personnel.git']])
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }

    stage('stop and delete old container') {
      steps {
        script {
          sh 'docker stop container'
          sh 'docker rm container'
        }

      }
    }

    stage('delete old images') {
      steps {
        script {
          sh 'docker image rm -f rami178/gestionpersonnel'
        }

      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh 'docker build -t rami178/gestionpersonnel .'
        }

      }
    }

    stage('Push image to dockerhub and Deploy the Application') {
      parallel {
        stage('Push Image to Dockerhub') {
          steps {
            script {
              withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                sh 'docker login -u rami178 -p ${dockerhubpwd}'
                sh 'docker push rami178/gestionpersonnel'
              }
            }

          }
        }

        stage('Run Container') {
          steps {
        sh 'docker run -d --name container -p 8087:8080 rami178/gestionpersonnel'
          }
        }

      }
    }

  }
  tools {
    maven 'Maven-3.9.6'
  }
  environment {
    DATE = new Date().format('yy.M')
    TAG = "${DATE}.${BUILD_NUMBER}"
  }
}
