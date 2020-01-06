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
              app.push("1.1.2.${env.BUILD_NUMBER}")
              app.push("latest")
            }
          }
        }
      }
    }
    
    stage('analyze with anchore'){
      steps {
        script {
          echo "gcr.io/kenna-experimental-datacenter/hartpence-test:latest > anchore_images"
          sh 'cat anchore_images'
          anchore name: 'anchore_images'
        }
      }
    }
  }
}
