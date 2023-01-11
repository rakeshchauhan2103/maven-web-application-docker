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

  stage('Build'){
  steps{
  sh  "mvn clean install package"
  }
  }
  
/*  stage("Publish to Nexus Repository Manager") {
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
        }*/
    
	stage('SonarQube analysis') {
    		environment {
      			SCANNER_HOME = tool 'SONAR'
    		}
    		steps {
    			withSonarQubeEnv(credentialsId: 'Sonarqube', installationName: 'SONAR') {
         			sh '''$SCANNER_HOME/bin/sonar-scanner \
         			-Dsonar.projectKey=projectKey \
         			-Dsonar.projectName=projectName \
         			-Dsonar.sources=src/ \
         			-Dsonar.java.binaries=target/classes/ \
         			-Dsonar.exclusions=src/test/java/****/*.java \
         			-Dsonar.java.libraries=/var/lib/jenkins/.m2/**/*.jar \
         			-Dsonar.projectVersion=${BUILD_NUMBER}-${GIT_COMMIT_SHORT}'''
       			}
     		}
	}
   /*	stage('SQuality Gate') {
     		steps {
       			timeout(time: 1, unit: 'MINUTES') {
       				waitForQualityGate abortPipeline: true
       			}
  		}
	}*/
	
}//stages closing

  }// pipeline closing
