node('DOTNETCORE'){
    
    parameters {
        booleanParam(name: 'RC', defaultValue: false, description: 'Is this a Release Candidate?')
    }
    environment {
        VERSION = "0.1.0"        
        VERSION_RC = "rc.2"
    }
    
	
		stage('SCM'){
		checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/pothagani-devops/jd']]])
		}
		
        stage('Audit tools') {                        
            
                auditTools()
            
        }
        stage('Build') {
            environment {
                VERSION_SUFFIX = getVersionSuffix()
            }
            
              echo "Building version: ${VERSION} with suffix: ${VERSION_SUFFIX}"
			  try{
              sh 'dotnet build -p:VersionPrefix="${VERSION}" --version-suffix "${VERSION_SUFFIX}" ./src/Pi.Web/Pi.Web.csproj'
			  } finally{
			  archiveArtifacts artifacts: 'src/*.*'
			  }
			  
            
        }
        stage('Unit Test') {
            
              dir('./src') {
                sh '''
                    dotnet test --logger "trx;LogFileName=Pi.Math.trx" Pi.Math.Tests/Pi.Math.Tests.csproj
                    dotnet test --logger "trx;LogFileName=Pi.Runtime.trx" Pi.Runtime.Tests/Pi.Runtime.Tests.csproj
                '''
                mstest testResultsFile:"**/*.trx", keepLongStdio: true
              }
            
        }
        stage('Smoke Test') {
            
              sh 'dotnet ./src/Pi.Web/bin/Debug/netcoreapp3.1/Pi.Web.dll'
            
        }
        stage('Publish') {
            when {
                expression { return params.RC }
            } 
            
                sh 'dotnet publish -p:VersionPrefix="${VERSION}" --version-suffix "${VERSION_RC}" ./src/Pi.Web/Pi.Web.csproj -o ./out'
                archiveArtifacts('out/')
            
        }
		stage('Archive'){
			archiveArtifacts artifacts: 'src/*.*'
		}
    }


String getVersionSuffix() {
    if (params.RC) {
        return env.VERSION_RC
    } else {
        return env.VERSION_RC + '+ci.' + env.BUILD_NUMBER
    }
}

void auditTools() {
    sh '''
        git version
        dotnet --list-sdks
        dotnet --list-runtimes
    '''
}