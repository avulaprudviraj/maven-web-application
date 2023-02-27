node
{
echo "The job name is ${env.JOB_NAME}"
echo "The build number is ${env.BUILD_NUMBER}"
echo "Node name is: ${env.NODE_NAME}"
echo "Jenkins Home dir is: ${env.JENKINS_HOME}"
def mavenHome = tool name :"maven3.8.7"
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
