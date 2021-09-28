pipeline {
  environment {
    registry = "registry.dev.rafay-edge.net:5000/rafay/kubelet-rubber-stamp"
    registryCredential = 'rcloud_user_registry.stage.rafay.cloud'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Building image') {
      steps{
        script {
          tag = "${env.GIT_BRANCH}"
           tags = tag.split("/")
          if (tags.size() == 3) {
            tag = tags[2]
          } else {
            tag = tags[1]
          }
          tag = tag + "-" + "${env.BUILD_NUMBER}"
          dockerImage = docker.build("registry.dev.rafay-edge.net:5000/rafay/kubelet-rubber-stamp:" + tag, "--build-arg BUILD_USR=${env.BUILD_USER} --build-arg BUILD_PWD=${env.BUILD_PASSWORD} .")
        }
      }
    }
    stage('Pushning image') {
      steps{
         script {
            docker.withRegistry('', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
  }
  post {
    success {
      slackSend channel: "#build",
      color: 'good',
      message: "Build ${currentBuild.fullDisplayName} completed successfully."
    }
    failure {
      slackSend channel: "#build",
      color: 'RED',
      message: "Attention ${env.JOB_NAME} ${env.BUILD_NUMBER} has failed."
    }
    always {
            deleteDir()
            dir("${workspace}@tmp") {
                deleteDir()
            }
            dir("${workspace}@script") {
                deleteDir()
        }
    }
  }
}