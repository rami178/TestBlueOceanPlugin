pipeline {
    agent any
    tools{
        maven 'Maven-3.9.6'
    }
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
    }
    stages {
        stage ('Build') {
            steps {
              checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rami178/Gestion-Personnel.git']])
                sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Clean old Image') {
            steps {
                script { 
                    def imageName = "${registry}" + "/" + "${branchName}"
                    env.imageName = "${imageName}"
                    def oldImageID = sh( 
                                            script: 'docker images -qf reference=\${imageName}:\${imageTag}',
                                            returnStdout: true
                                        )

                    echo "Image Name: " + "${imageName}"
                    echo "Old Image: ${oldImageID}"

                    if ( "${oldImageID}" != '' ) {
                        echo "Deleting image id: ${oldImageID}..."
                        sh "docker rmi -f $oldImageID"
                    } else {
                        echo "No image to delete..."
                        } 
                    }  
                }
            }
  
        stage ('Build Docker Image') {
            steps {
                script{
                sh 'docker build -t rami178/gestionpersonnel .'
                }
            }
        }
           stage ('Push Docker Image') {
            steps {
                script{
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                 sh 'docker login -u rami178 -p ${dockerhubpwd}'
                    sh 'docker push rami178/gestionpersonnel'
                  }
                }
            }
        }
            stage('Run') {
            steps {
                sh 'docker run -d --name container -p 8087:8080 rami178/gestionpersonnel'
            }
        }
  }
}
