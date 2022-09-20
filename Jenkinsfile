node{
  def mavenHome = tool name: 'maven3.8.6'
  stage('1cloneCode'){
     git "https://github.com/bisiman2/tesla-app"
  }
  stage('2Test&Build'){
    sh "${mavenHome}/bin/mvn clean package"
    //bat "${mavenHome}/bin/mvn clean package"
  }
  stage('3codeQuality'){
    sh "${mavenHome}/bin/mvn sonar:sonar"
  }
  stage('4uploadArtifacts'){
    sh "${mavenHome}/bin/mvn deploy"
  }
    stage('5deploy2UAT'){
    sh "echo 'deploy to UAT'  "
    deploy adapters: [tomcat9(credentialsId: 'tomcat9-credentials', path: '', url: 'http://34.209.159.10:8080/')], contextPath: null, war: 'target/*war'
  }
    stage('6approvalGate'){
    sh "echo 'ready for review' "
    timeout(time:2, unit:'MINUTES'){
    input message: 'Application ready for deployment, Please review and approve'
      }
  }
    stage('7deploy2Prod'){
    deploy adapters: [tomcat9(credentialsId: 'tomcat9-credentials', path: '', url: 'http://34.209.159.10:8080/')], contextPath: null, war: 'target/*war'    
  }
    stage('8emailNotification'){
    emailext body: '''Hi All,
    
Kindly check build status.
Bisman2''', recipientProviders: [buildUser(), developers(), upstreamDevelopers(), brokenBuildSuspects(), brokenTestsSuspects(), contributor()], subject: 'build status', to: 'tesla-app@gmail.com'    
  }
}
