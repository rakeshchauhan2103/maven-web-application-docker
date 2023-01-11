pipeline{

agent any
	
tools{
	maven 'MAVEN'
}

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
	
	stage('SonarQube analysis') {
    		environment {
      		SCANNER_HOME = tool 'SONAR'
    		}
    		steps {
    			withSonarQubeEnv(credentialsId: 'Sonar-token', installationName: 'SONAR') {
         			sh '$SCANNER_HOME/bin/sonar-scanner \
         			-Dsonar.projectKey=maven-demo-webapp \
         			-Dsonar.projectName=maven-demo-webapp \ '
       			}
     		}
  	}
	
	
	
	
}
}
