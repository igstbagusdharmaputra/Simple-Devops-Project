node{
   stage('SCM Checkout'){
     git 'https://github.com/igstbagusdharmaputra/Simple-Devops-Project'
   }
   stage('Compile-Package'){
      // Get maven home path
      //def mvnHome =  tool name: 'maven-3', type: 'maven'   
      sh "mvn package"
   }
   stage('slack notification') {
     slackSend teamDomain: 'cicd-oao6171', 
     tokenCredentialId: 'slack-token'
   }
   stage('Deploy to Tomcat'){
      sshagent(['tomcat']){
         sh 'sudo scp -o StrictHostKeyChecking=no target/*.war ubuntu@172.31.52.35:/opt/tomcat/latest/webapps/'
      }
   }
}
