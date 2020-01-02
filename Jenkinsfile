def project = 'kenna-experimental-datacenter'
def  appName = 'hartpence-test'
def  feSvcName = "${appName}-frontend"
def  imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

pipeline {
  stages {
    stage('Build and push image with Container Builder') {
      steps {
        container('docker') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${imageTag} ."
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



