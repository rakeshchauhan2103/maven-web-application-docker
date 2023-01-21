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

		stage('deploy'){
			steps{
				sshPublisher(publishers: [sshPublisherDesc(configName: 'docker ', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'cd webapp && docker build -t .', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'webapp', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/*')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
			}
		}



	} //stages close
} //pipeline close
