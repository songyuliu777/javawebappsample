node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=6a8d66f3-2ea4-4126-b9e9-05025450edf1',
    'AZURE_TENANT_ID=41c25965-d8ea-4663-80c1-ad36009c518a',
    'AZURE_RESOURCE_GROUP=jenkins-get-started-rg',
    'AZURE_WEBAPP_NAME=songyu-jenkins-calculator-2026'
  ]) {
    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      withCredentials([
        usernamePassword(
          credentialsId: 'AzureServicePrincipal',
          usernameVariable: 'AZURE_CLIENT_ID',
          passwordVariable: 'AZURE_CLIENT_SECRET'
        )
      ]) {
        try {
          sh '''
            set +x
            az login --service-principal \
              --username "$AZURE_CLIENT_ID" \
              --password="$AZURE_CLIENT_SECRET" \
              --tenant "$AZURE_TENANT_ID" \
              --output none

            az account set \
              --subscription "$AZURE_SUBSCRIPTION_ID"

            az webapp deploy \
              --resource-group "$AZURE_RESOURCE_GROUP" \
              --name "$AZURE_WEBAPP_NAME" \
              --src-path target/calculator-1.0.war \
              --type war
          '''
        } finally {
          sh 'az logout'
        }
      }
    }
  }
}
