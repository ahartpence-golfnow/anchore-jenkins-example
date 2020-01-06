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
            docker.withRegistry("https://gcr.io", "gcr:gcr-kenna-experimental"){
              app.push()
            }
          }
        }
      }
    }
    
    stage('run anchore image secret scan' {
      steps {
        container('') {
          
        }
      }
    }
  }
}
