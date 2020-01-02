def project = 'kenna-experimental-datacenter'
def  appName = 'hartpence-test'
def  feSvcName = "${appName}-frontend"
def  imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

pipeline {
  environment {
    registry = "ahartpence/test"
    registryCredential = 'hartpence-docker-test'
  }
  
  agent {any {}}
  stages {
    stage('Building image') {
      steps{
        container('docker'){
         script {
          docker.build registry + ":$BUILD_NUMBER"
      }
     }    
    }
  }
    
    stage('testttt') {
      steps {
        container('docker') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${imageTag} ."
        }
      }
    }  
  }
}



