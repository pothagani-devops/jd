node('DOTNETCORE'){
	stage('SCM'){
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/pothagani-devops/jd']]])
	}
	stage('Build'){
		
		sh 'dotnet build ConsoleApp1'
		
		archiveArtifacts artifacts: 'ConsoleApp1/*.*'
		
	}
	stage('Test'){
		echo 'Execute unit tests'
	}
	stage('Package'){
		echo 'Zip it up'
	}
	stage('Deploy'){
		echo 'Push to deployment'
	}
	stage('Archive'){
		archiveArtifacts artifacts: 'ConsoleApp1/*.*'
	}
}
