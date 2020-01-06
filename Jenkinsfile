def project = 'kenna-experimental-datacenter'
def  appName = 'hartpence-test'
def  feSvcName = "${appName}-frontend"
def  imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

pipeline {
  
  agent {any {}}
  
  stages {
    stage('Building image') {
      steps{
        container('docker'){
          script {
            app = docker.build("kenna-experimental-datacenter/hartpence-test")
        }
      }    
    }
  }
    
    stage('push to gcr') {
      steps {
        container('docker') {
          script {
              withCredentials([string(credentialsId: 'kenna-experimental-datacenter', variable: 'docker-creds')]) {
                 sh /* WRONG! */ """
                  set +x
                  echo $docker-creds |  docker login -u _json_key --password-stdin https://gcr.io
                """
             }
          }
        }
      }
    }  
  }
}
