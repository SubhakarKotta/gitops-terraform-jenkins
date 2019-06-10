pipeline {
  agent any

  environment {
    PLAY_ACTIVATOR = tool name: 'Terraform', type: 'com.cloudbees.jenkins.plugins.customtools.CustomTool'
  }

  stages {
    stage('Plan') {
      steps {
        sh "${PLAY_ACTIVATOR}/terraform --version"
      }
    }
  }
}