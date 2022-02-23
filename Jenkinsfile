pipeline {
  environment {
      registry = "repository/image-repo"
      registryCredential = 'nexus-admin'
    //registry = "tamizh2022/jenkins-docker-nexus"
    //registryCredential = 'docker-admin'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git 'https://github.com/tamilzh2018/demo-nodejs-app.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( 'http://192.168.43.89:8081/', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
