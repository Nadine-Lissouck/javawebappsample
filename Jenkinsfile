import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=0204ac69-8342-4e69-8716-aecb5b5ccfa8',
        'AZURE_TENANT_ID=36da45f1-dd2c-4d1f-af13-5abe46b99921']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'PW-EnterpriseSPC-ResourceGroup'
      def webAppName = 'oacis-react-webapp-devops-test'
      // login Azure
          az webapp deploy --resource-group PW-EnterpriseSPC-ResourceGroup --name oacis-react-webapp-devops-test --src-path ./build.zip

      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
