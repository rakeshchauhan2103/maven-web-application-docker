pipeline{

agent any
	
tools{
	maven 'MAVEN'
}
	
environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "3.83.64.77:8081"
        NEXUS_REPOSITORY = "maven-demo-testing"
        NEXUS_CREDENTIAL_ID = "Nexus"
	DOCKERHUB = credentials("Dockerhub")
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
         			sh '''$SCANNER_HOME/bin/sonar-scanner \
         			-Dsonar.projectKey=testing \
         			-Dsonar.projectName=testing \
         			-Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
       			}
     		}
  	}
	
	stage('Build'){
  		steps{
  			sh  "mvn clean install package"
  		}
  	}
  
	stage("Publish to Nexus Repository Manager") {
        	steps {
                	script {
                    		pom = readMavenPom file: "pom.xml";
                    		filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    		echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    		artifactPath = filesByGlob[0].path;
                    		artifactExists = fileExists artifactPath;
                    		if(artifactExists) {
                        		echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        		nexusArtifactUploader(
                            			nexusVersion: NEXUS_VERSION,
                            			protocol: NEXUS_PROTOCOL,
                            			nexusUrl: NEXUS_URL,
                            			groupId: pom.groupId,
                            			version: pom.version,
                            			repository: NEXUS_REPOSITORY,
                            			credentialsId: NEXUS_CREDENTIAL_ID,
                            			artifacts: [
                                			[artifactId: pom.artifactId,
                                			classifier: '',
                                			file: artifactPath,
                                			type: pom.packaging],
                                			[artifactId: pom.artifactId,
                                			classifier: '',
                                			file: "pom.xml",
                                			type: "pom"]
                            			]
                        		);
                    		} 
		    		else {
                    			error "*** File: ${artifactPath}, could not be found";
                    		}
                	}
            	}
        }
  
 	stage('Build Docker Image'){
		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'dev', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd maven-project-demo && docker build -t webapp .', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  

		}
  	}
	
	stage('Pushing Image to DockerHub'){
		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'dev', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'echo $DOCKERHUB_PSW | sudo docker login -u $DOCKERHUB_USR --password-stdin && docker tag webapp:latest pritidevops/webapp:latest && docker push pritidevops/webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
		}
	}
  
	stage('Staging'){
  		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'staging', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker stop web-app && docker rm web-app && cd maven-project-demo && docker pull pritidevops/webapp:latest && docker run -itd -p 8080:8080 --name web-app webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  		}
  	}
	
	stage('Pre-Prod Approval'){
  		steps{              
        		script {
                    		timeout(time: 10, unit: 'MINUTES'){
                        		input ('Deploy to Production?')
                    			mail body: "'<a href='${BUILD_URL}input'>click to approve</a>'",
                   			cc: '', 
                    			charset: 'UTF-8', 
                    			from: 'pritiranga9@gmail.com', 
                    			mimeType: 'text/html', 
                    			replyTo: 'sureshranga1777@gmail.com', 
                    			subject: "Waiting for your Approval! Job: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", 
                    			to: "sureshranga1777@gmail.com";            
                    		}
                	}        
  		} 
  	}
	
	stage('Production'){
  		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'prod', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'docker stop web-app && docker rm web-app && cd maven-project-demo && docker build -t webapp . && docker run -itd -p 8080:8080 --name web-app webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  		}
  	}
}//stages close	

post{

	success{
 		mail body: "<b>Build Result</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL of build: ${env.BUILD_URL} ", 
        	cc: '', 
        	charset: 'UTF-8', 
        	from: 'pritiranga9@gmail.com', 
        	mimeType: 'text/html', 
        	replyTo: 'sureshranga1777@gmail.com', 
        	subject: "Job: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", 
        	to: "sureshranga1777@gmail.com"; 
 	}
 
	failure{
 		mail body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
        	cc: '', 
        	charset: 'UTF-8', 
        	from: 'pritiranga9@gmail.com', 
        	mimeType: 'text/html', 
        	replyTo: 'sureshranga1777@gmail.com', 
        	subject: "Job: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", 
        	to: "sureshranga1777@gmail.com"; 
 	}
 }	 
	
} //pipeline close
