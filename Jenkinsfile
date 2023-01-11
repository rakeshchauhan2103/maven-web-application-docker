pipeline{

agent any

tools{
	maven 'MAVEN'
}

environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "44.201.102.243:8081"
        NEXUS_REPOSITORY = "maven-demo-repo"
        NEXUS_CREDENTIAL_ID = "Nexus"
}

stages{

	stage('SonarQube analysis') {
    		environment {
      		SCANNER_HOME = tool 'SONAR'
    		}
    		steps {
    			withSonarQubeEnv(credentialsId: 'Sonarqube', installationName: 'SONAR') {
         			sh '''$SCANNER_HOME/bin/sonar-scanner \
         			-Dsonar.projectKey=maven-demo-webapp-analysis \
         			-Dsonar.projectName=maven-demo-webapp-analysis \
         			-Dsonar.sources=src/ \
         			-Dsonar.java.binaries=target/classes/ \
         			-Dsonar.exclusions=src/test/java/****/*.java \
         			-Dsonar.java.libraries=/var/lib/jenkins/.m2/**/*.jar \
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
  
	stage('Dev'){
  		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'dev', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd maven-project-demo && docker build -t webapp . && docker run -itd -p 8080:8080 webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  		}
  	}
  
	stage('Staging'){
  		steps{
			sshPublisher(publishers: [sshPublisherDesc(configName: 'staging', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd maven-project-demo && docker build -t webapp . && docker run -itd -p 8080:8080 webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  		}
  	}
  	
  	stage('Pre-Prod Approval'){
  		steps{              
        		script {
                    		timeout(time: 1, unit: 'MINUTES'){
                        		def deploymentDelay = input id: 'Deploy', message: 'Deploy to production?', parameters: [choice(choices: ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12', '13', '14', '15', '16', '17', '18', '19', '20', '21', '22', '23', '24'], description: 'Hours to delay deployment?', name: 'DeploymentDelay')]
                    			sleep time: DeploymentDelay.toInteger(), unit: 'HOURS'
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
			sshPublisher(publishers: [sshPublisherDesc(configName: 'prod', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd maven-project-demo && docker build -t webapp . && docker run -itd -p 8080:8080 webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  		}
  	}
	
  } //stages closing

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
} // pipeline closing
