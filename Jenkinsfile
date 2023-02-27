node
{
echo "The job name is ${env.JOB_NAME}"
echo "The build number is ${env.BUILD_NUMBER}"
echo "Node name is: ${env.NODE_NAME}"
echo "Jenkins Home dir is: ${env.JENKINS_HOME}"
def mavenHome = tool name :"maven3.8.7"
try{
stage('ChekcoutCode'){
git branch: 'development', credentialsId: 'f3b26d4d-8d92-459d-a58c-3104193a58a3', url: 'https://github.com/avulaprudviraj/maven-web-application.git'
}
stage('Build'){
sh "${mavenHome}/bin/mvn clean package sonar:sonar deploy"
}
// stage('sonarqube report'){
// sh "${mavenHome}/bin/mvn clean sonar:sonar"
// }
// stage('Nexusrepository'){
// sh "${mavenHome}/bin/mvn clean deploy"
// }
stage('Tomcat deploy'){
deploy adapters: [tomcat9(credentialsId: '8175ff45-ac30-40a2-b76a-5f9181da53df', path: '', url: 'http://3.108.52.35:9980/')], contextPath: null, war: 'target/maven-web-application.war'
}

}
  catch (e) {
    // If there was an exception thrown, the build failed
    
    currentBuild.result = "FAILED"
    throw e
  } 
finally {
    // Success or failure, always send notifications

    sendSlackNotification(currentBuild.result)
  }
}
def sendSlackNotification(String buildStatus = 'STARTED') {

  // build status of null means successful

  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {

    colorName = 'YELLOW'
    colorCode = '#FFFF00'

  } else if (buildStatus == 'SUCCESS') {
    colorName = 'GREEN'
    colorCode = '#00FF00'
  } else {
    colorName = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
