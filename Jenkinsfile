def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

node {
    echo "Job Name is:  ${env.JOB_NAME}"
    echo "Node Name is:  ${env.NODE_NAME}"

    def mavenHome = tool name: 'Maven3.8.5'
   try{ 
    notifyBuild('STARTED')
    stage('Checkout Code'){
        git branch: 'development', credentialsId: '9c9baa7b-c6ba-4f8a-b1ef-9fad5e7e8e79', url: 'https://github.com/renish03/maven-web-application.git'
     }
    stage('build'){
        sh "${mavenHome}/bin/mvn clean package"
    }
    // Execute Sonarqube scan
    stage('Execute Sonarqube'){
      sh "${mavenHome}/bin/mvn sonar:sonar "
    }
    stage('Upload Artifacts'){
      sh "${mavenHome}/bin/mvn deploy "
    }
    /*
    stage('Upload to tomcat'){
       sshagent(['TomCat2']) {
   	   sh "scp –o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.233.157.250:/opt/apache-tomcat-9.0.62/webapps/"
      }
      
    }*/
   } // Try Block Closing
  catch(e){
    currentBuild.result = "FAILED"
  }
  finally{
    notifyBuild(currentBuild.result)
  }

}
