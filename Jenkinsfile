pipeline{
	agent any
	tools{
		maven "Maven"
	} 
	stages{
		stage('build'){
			steps{
				sh"mvn clean install package"
			}
		}
	} //stages close
} //pipeline close
