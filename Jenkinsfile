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
         			sh "mvn sonar:sonar\
				-Dsonar.host.url= http://100.25.102.147:9000/\
				-Dsonar.projectKey= testing"
       			}
     		}
  	}
	
	
	
	
}
}
