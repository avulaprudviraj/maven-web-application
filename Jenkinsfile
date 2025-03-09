/*node
{
echo "The job name is ${env.JOB_NAME}"
echo "The build number is ${env.BUILD_NUMBER}"
echo "Node name is: ${env.NODE_NAME}"
echo "Jenkins Home dir is: ${env.JENKINS_HOME}"
def mavenHome = tool name :"maven3.8.7"
try{
sendSlackNotification('STARTED')
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
  slackSend (color: colorCode, message: summary, channel:'#hdfcbank')
  
} */

pipeline{
    agent any
    tools{
        maven 'Maven 3.9.9'
        
    }
    triggers {
  githubPush()
   }

    environment{
        
        Image_Tag = "${BUILD_NUMBER}"
        Image_Name = "dockerhandson302/maven-web-application"
        Remote_User = "ubuntu"
        Remote_Host = "172.31.4.81"
        
    }
    stages{
        stage('checking code form git'){
            steps{
                git credentialsId: 'Github_credentials', url: 'https://github.com/avulaprudviraj/maven-web-application.git'
            }
        }
        stage('Build the package of war file'){
            steps{
                sh "mvn -version"
                sh "mvn clean package"
            }
        }
        stage('Building the image by using docker'){
            steps{
                sh "docker build -t ${Image_Name}:${Image_Tag} ."
                sh "ls -lrt"
                
            }
        }
        stage('login in to dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'dockerpassword', variable: 'Password')]) {
                    
                 sh "docker login -u dockerhandson302 -p ${Password}"
                }
            }
        }
        stage('Push image in to docker hub'){
            steps{
                sh "docker push ${Image_Name}:${Image_Tag}"
            }
        }
        stage('Creating a container in deployment server'){
            steps{
                sshagent(['Docker']) {

                  sh "ssh StrictHostKeyChecking=no ${Remote_User}@${Remote_Host} ls -lrt"
                 
                 sh "ssh StrictHostKeyChecking=no ${Remote_User}@${Remote_Host} docker rm -f flipkartcnt || true"
                 sh "ssh ${Remote_User}@${Remote_Host} docker run -d --name flipkartcnt -p 8080:8080 ${Image_Name}:${Image_Tag}"
                }
            }
        }
    }
}

