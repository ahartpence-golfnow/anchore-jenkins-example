  agent {
    kubernetes {
      label 'sample-app'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: golang
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""



stage('Configure') {
    abort = false
    inputConfig = input id: 'InputConfig', message: 'Docker registry and Anchore Engine configuration', parameters: [string(defaultValue: 'https://index.docker.io/v1/', description: 'URL of the docker registry for staging images before analysis', name: 'dockerRegistryUrl', trim: true), string(defaultValue: 'docker.io', description: 'Hostname of the docker registry', name: 'dockerRegistryHostname', trim: true), string(defaultValue: '', description: 'Name of the docker repository', name: 'dockerRepository', trim: true), credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl', defaultValue: '', description: 'Credentials for connecting to the docker registry', name: 'dockerCredentials', required: true), string(defaultValue: '', description: 'Anchore Engine API endpoint', name: 'anchoreEngineUrl', trim: true), credentials(credentialType: 'com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl', defaultValue: '', description: 'Credentials for interacting with Anchore Engine', name: 'anchoreEngineCredentials', required: true)]

    for (config in inputConfig) {
        if (null == config.value || config.value.length() <= 0) {
          echo "${config.key} cannot be left blank"
          abort = true
        }
    }

    if (abort) {
        currentBuild.result = 'ABORTED'
        error('Aborting build due to invalid input')
    }
}

node {
  def app
  def dockerfile
  def anchorefile
  def repotag

  try {
    stage('Checkout') {
      // Clone the git repository
      checkout scm
      def path = sh returnStdout: true, script: "pwd"
      path = path.trim()
      dockerfile = path + "/Dockerfile"
      anchorefile = path + "/anchore_images"
    }

    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${imageTag} ."
        }
      }
    }
 
      stage('Parallel') {
      parallel Test: {
        app.inside {
            sh 'echo "Dummy - tests passed"'
        }
      },
      Analyze: {
        writeFile file: anchorefile, text: inputConfig['dockerRegistryHostname'] + "/" + repotag + " " + dockerfile
        anchore name: anchorefile, engineurl: inputConfig['anchoreEngineUrl'], engineCredentialsId: inputConfig['anchoreEngineCredentials'], annotations: [[key: 'added-by', value: 'jenkins']]
      }
    }
  } finally {
    stage('Cleanup') {
      // Delete the docker image and clean up any allotted resources
      sh script: "docker rmi " + repotag
    }
  }
}
