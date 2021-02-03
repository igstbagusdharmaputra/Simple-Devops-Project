// node{
//    stage('SCM Checkout'){
//      git 'https://github.com/igstbagusdharmaputra/Simple-Devops-Project'
//    }
//    stage('Compile-Package'){
//       // Get maven home path
//       //def mvnHome =  tool name: 'maven-3', type: 'maven'   
//       sh "mvn package"
//    }
//    stage('slack notification') {
//      slackSend teamDomain: 'cicd-oao6171', 
//      tokenCredentialId: 'slack-token'
//    }
//    stage('Deploy to Tomcat'){
//       sshagent(['tomcat']){
//          sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@172.31.52.35:/opt/tomcat/latest/webapps'
//       }
//    }
// }

pipeline{
   agent any
   tools {
      maven 'maven3'
   }
   environment {
      DOCKER_TAG = getVersion()
   }
   stages {
      stage ('SCM'){
         steps{
            git credentialsId: 'account-github', 
            url: 'https://github.com/igstbagusdharmaputra/Simple-Devops-Project'
         }
      }
      stage('Maven Build'){
         steps{
            sh 'mvn clean package'
         }
      }
      stage('Docker Build'){
         steps{
            sh 'docker build -t dharmatkj/maven:${DOCKER_TAG} .'
         }
      }
      stage('Docker Push'){
         steps{
            withCredentials([string(credentialsId:'docker-hub',variable:'dockerpassword')]){
               sh 'docker login -u dharmatkj -p ${dockerpassword}'
            }
            sh 'docker push dharmatkj/maven:${DOCKER_TAG}'
         }
      }
      stage('Docker Deploy'){
         steps{
            ansiblePlaybook credentialsId: 'tomcat', disableHostKeyChecking: true,  extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'hosts', playbook: 'deploy.yml'
         }
      }
   }
}
def getVersion(){
   def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
   return commitHash
}