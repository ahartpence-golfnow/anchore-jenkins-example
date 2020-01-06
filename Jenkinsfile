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
    
    stage('analyize with anchore'){
      steps {
        parallel(
          prep-for-anchore: {
            echo "1.1.1.2${env.BUILD_NUMBER} ${workspace}/Dockerfile > anchore_images"
          },
          analyze: {
            anchore name: 'anchore_images'
          }

        )
      }
    }
  }
}
