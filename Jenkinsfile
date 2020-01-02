def project = 'kenna-experimental-datacenter'
def  appName = 'hartpence-test'
def  feSvcName = "${appName}-frontend"
def  imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

pipeline {
  environment {
    registry = "hartpence-test"
    registryCredential = 'hartpence-docker-test'
  }
  
  agent {any {}}
  stages {
    stage('Building image') {
      steps{
        container('docker'){
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }    
    }
  }
    
    stage('push to gcr') {
      steps {
        container('docker') {
          script {
            docker.withRegistry( 'gcr.io/kenna-experimental-datacenter/hartpence-test', registryCredential ) {
            dockerImage.push()
            }
          }
        }
      }
    }  
  }
}



