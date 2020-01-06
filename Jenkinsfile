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
      steps{
        Analyze: {
          writeFile file: anchorefile, text: inputConfig['dockerRegistryHostname'] + "/" + repotag + " " + dockerfile
          anchore name: anchorefile, engineurl: inputConfig['anchoreEngineUrl'], engineCredentialsId: inputConfig['anchoreEngineCredentials'], annotations: [[key: 'added-by', value: 'jenkins']]
        }
      }
    }
  }
}
