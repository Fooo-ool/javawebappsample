pipeline {
  agent any

  environment {
    // ⚠️ 这里必须是你的订阅ID和租户ID（你上次把租户填成了订阅）
    AZURE_SUBSCRIPTION_ID = '37cb5328-946c-45d4-b9b5-08cc87abb52f'
    AZURE_TENANT_ID       = 'ed0e4668-1c46-42a6-8d5c-d74e4743cbb9'
    RESOURCE_GROUP        = 'jenkins-get-started-rg'
    WEBAPP_NAME           = 'jiajin-cc8'
  }

  stages {
    stage('init') {
      steps {
        checkout scm
      }
    }

    stage('build') {
      steps {
        sh 'mvn -version'
        sh 'mvn clean package -DskipTests'
      }
    }

    stage('deploy') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'AzureServicePrincipal', // Jenkins里保存的SP凭据ID
          usernameVariable: 'AZURE_CLIENT_ID',
          passwordVariable: 'AZURE_CLIENT_SECRET'
        )]) {
          sh '''
            set -e
            az login --service-principal -u "$AZURE_CLIENT_ID" -p "$AZURE_CLIENT_SECRET" --tenant "$AZURE_TENANT_ID"
            az account set --subscription "$AZURE_SUBSCRIPTION_ID"
            WAR=$(ls target/*.war | head -n 1)
            az webapp deploy -g "$RESOURCE_GROUP" -n "$WEBAPP_NAME" --type war --src-path "$WAR"
            az logout
          '''
        }
      }
    }
  }
}

