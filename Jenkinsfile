pipeline{

agent any

tools{
maven 'MAVEN'

}

stages{

  stage ('code'){
	  steps{
		  script{
			checkout scmGit(
    				branches: [[name: 'testing']],
    				userRemoteConfigs: [[url: 'https://github.com/pritiranga/maven-web-application-docker.git']])
		  }
	  }
  
  }
	
}
}
