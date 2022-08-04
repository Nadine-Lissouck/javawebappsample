import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=c696cece-97b8-4c14-8207-828cacab5433',
        'AZURE_TENANT_ID=36da45f1-dd2c-4d1f-af13-5abe46b99921']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'PWEnterpriseSPCResourceGroup_Dev'
      def webAppName = 'P5-React-Web-App-5433'
      // login Azure
          az login --use-device-code
          az webapp up --name "P5-React-Web-App-5433" --plan "AppServicePlan-P5-React-Web-App-5433"
          az account set -s $AZURE_SUBSCRIPTION_ID
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
         '''
}
