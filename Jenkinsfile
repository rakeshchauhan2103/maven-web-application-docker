pipeline{

agent any

stages{

	stage ('Source code checkout'){
		steps{
			script{
				checkout scmGit(
    					branches: [[name: 'testing']],
    					userRemoteConfigs: [[url: 'https://github.com/pritiranga/maven-web-application-docker.git']]
		  		)
			}
	  	}
   	}
	
	
	
	
}
}
