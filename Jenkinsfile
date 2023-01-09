pipeline{

agent any

tools{
maven 'MAVEN'

}

stages{

  stage('Build'){
  steps{
  sh  "mvn clean install package"
  }
  }
  
  /*stage('UploadArtifactsIntoNexus'){
  steps{
  sh  "mvn clean deploy"
  }
  }*/
  
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
	input("Approve or Reject")
  }
  }
	
  stage('Production'){
  steps{
	sshPublisher(publishers: [sshPublisherDesc(configName: 'prod', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd maven-project-demo && docker build -t webapp . && docker run -itd -p 8080:8080 webapp:latest', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'maven-project-demo', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])  
  }
  }
	
  }

}

/*post{

 success{
 emailext to: 'devopstrainingblr@gmail.com,mithuntechnologies@yahoo.com',
          subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
          body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
          replyTo: 'devopstrainingblr@gmail.com'
 }
 
 failure{
 emailext to: 'devopstrainingblr@gmail.com,mithuntechnologies@yahoo.com',
          subject: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
          body: "Pipeline Build is over .. Build # is ..${env.BUILD_NUMBER} and Build status is.. ${currentBuild.result}.",
          replyTo: 'devopstrainingblr@gmail.com'
 }
 
}


}//Pipeline closing
*/
