pipeline {
  agent any

  environment {
    ARM_CLIENT_ID       = ''
    ARM_CLIENT_SECRET   = ''
    ARM_SUBSCRIPTION_ID = ''
    ARM_TENANT_ID       = ''
  }

  /*stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/Mahender2485/azure-vmss-deploy.git', branch: 'main'
      }
    } */

    stages {
    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }

    stage('Load Azure Credentials and Run Terraform') {
      steps {
        withCredentials([string(credentialsId: 'AZURE_CREDENTIALS', variable: 'AZURE_CREDENTIALS_JSON')]) {
          script {
            def azureCreds = readJSON text: AZURE_CREDENTIALS_JSON

            withEnv([
              "ARM_CLIENT_ID=${azureCreds.clientId}",
              "ARM_CLIENT_SECRET=${azureCreds.clientSecret}",
              "ARM_SUBSCRIPTION_ID=${azureCreds.subscriptionId}",
              "ARM_TENANT_ID=${azureCreds.tenantId}"
            ]) {
              // ✅ All terraform commands go inside here
              sh 'terraform init'
              sh 'terraform plan -out=tfplan'
              sh 'terraform apply -auto-approve tfplan'
            }
          }
        }
      }
    }
    post {
    success {
      slackSend channel: '#jenkin-pipeline', message: "✅ *Build succeeded*: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
    }
    failure {
      slackSend channel: '#jenkin-pipeline', message: "❌ *Build failed*: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
    }
  }
}