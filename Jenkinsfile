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

pipeline {
    agent any
    
    tools {
  maven 'Maven 3.9.9'
    }
    environment{
        buildNumber = "${Build_Number}"
        
    }

    stages {
        stage('checkout code') {
            steps {
                echo 'Getting code from SCM'
                git credentialsId: 'Git', url: 'https://github.com/avulaprudviraj/maven-web-application.git'
                
            }
        }
        stage('Building the package') {
            steps {
                echo 'Building the package by using Maven Build Tool'
                sh "mvn clean package"
                
            }
        }
        stage('Building the image') {
            steps {
                echo 'Building the image by using Docker'
                sh "docker build -t dockerhandson302/maven-web-application:${buildNumber} ."
                sh "ls -lrt"
                
            }
        }
        stage('Login to Dokcer hub') {
            steps {
                withCredentials([string(credentialsId: 'Dockerpass', variable: 'Docker')]) {
                    
                sh "docker login -u dockerhandson302 -p ${Docker}"
                
                }
                
                
            }
        }
        stage('Push to Dokcer hub') {
            steps {
                echo 'Pushinh the image in to Dokcer hub'
                sh "docker push dockerhandson302/maven-web-application:${buildNumber}"
                
            }
        }
        stage('create a conatiner in deployment server') {
            steps {
                echo 'create a container'
                sshagent(['Docker_deplotment_server']) {
                  sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.3.106 docker rm -f mavencnt || true"
                  sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.3.106 docker run -d --name mavencnt -p 80:8080 dockerhandson302/maven-web-application:${buildNumber}"
                }
                
            }
        }
    }
}

