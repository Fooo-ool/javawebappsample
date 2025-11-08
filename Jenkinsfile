import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=37cb5328-946c-45d4-b9b5-08cc87abb52f',
    'AZURE_TENANT_ID=ed0e4668-1c46-42a6-8d5c-d74e4743cbb9'
  ]) {
    
    stage('init') {
      echo "==== INIT STAGE ===="
      checkout scm
    }
  
    stage('build') {
      echo "==== BUILD STAGE ===="
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      echo "==== DEPLOY STAGE ===="
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName   = 'jiajin-cc8'
      
      // ✅ 用你创建的 Service Principal 凭据 ID
      withCredentials([usernamePassword(
        credentialsId: 'AzureServicePrincipal', 
        usernameVariable: 'AZURE_CLIENT_ID', 
        passwordVariable: 'AZURE_CLIENT_SECRET'
      )]) {

        // ✅ 登录 Azure
        sh '''
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }

      // ✅ 修复 PHP 环境问题：改成 Java 11 + Tomcat 9
      sh """
        az webapp config set \
          -g ${resourceGroup} \
          -n ${webAppName} \
          --linux-fx-version "TOMCAT|9.0-java11"
      """

      // ✅ 部署 WAR 文件
      sh """
        az webapp deploy \
          --resource-group ${resourceGroup} \
          --name ${webAppName} \
          --src-path target/calculator-1.0.war \
          --type war
      """

      // ✅ 登出
      sh 'az logout'
    }
  }
}


