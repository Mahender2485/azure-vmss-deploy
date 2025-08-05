pipeline {
  agent any

  environment {
    ARM_CLIENT_ID       = ''
    ARM_CLIENT_SECRET   = ''
    ARM_SUBSCRIPTION_ID = ''
    ARM_TENANT_ID       = ''
  }

  stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/Mahender2485/azure-vmss-deploy.git', branch: 'main'
      }
    }

    stage('Load Azure Credentials') {
      steps {
        withCredentials([string(credentialsId: 'AZURE_CREDENTIALS_JSON', variable: 'AZURE_CREDENTIALS')]) {
          writeFile file: 'azure_creds.json', text: AZURE_CREDENTIALS
          script {
            def creds = readJSON file: 'azure_creds.json'
            env.ARM_CLIENT_ID       = creds.clientId
            env.ARM_CLIENT_SECRET   = creds.clientSecret
            env.ARM_SUBSCRIPTION_ID = creds.subscriptionId
            env.ARM_TENANT_ID       = creds.tenantId
          }
        }
      }
    }

    stage('Terraform Init') {
      steps {
        sh 'terraform init'
      }
    }

    stage('Terraform Plan') {
      steps {
        sh 'terraform plan -out=tfplan'
      }
    }

    stage('Terraform Apply') {
      steps {
        sh 'terraform apply -auto-approve tfplan'
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