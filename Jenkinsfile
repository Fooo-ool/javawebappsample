stage('deploy') {
  def resourceGroup = 'jenkins-get-started-rg'
  def webAppName   = 'jiajin-cc8'
  
  withCredentials([azureServicePrincipal(credentialsId: 'AzureServicePrincipal')]) {
    sh '''
      az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
      az account set -s $AZURE_SUBSCRIPTION_ID
    '''
  }

  // ✅ 修复 PHP 环境问题，设置为 Tomcat + Java 11
  sh """
    az webapp config set \
      -g ${resourceGroup} \
      -n ${webAppName} \
      --linux-fx-version "TOMCAT|9.0-java11"
  """

  // ✅ 部署 WAR 包
  sh """
    az webapp deploy \
      --resource-group ${resourceGroup} \
      --name ${webAppName} \
      --src-path target/calculator-1.0.war \
      --type war
  """

  sh 'az logout'
}

