pipeline {
	agent any
	stages {
		stage ('Getting from git') {
			steps {
				checkout([
					$class: 'GitSCM',
					branches: [[name: '${ghprbSourceBranch}']],
					doGenerateSubmoduleConfigurations: false,
					extensions: [],
					submoduleCfg: [],
					userRemoteConfigs: [[
						credentialsId: '7303d04c-541b-49a3-83f2-834b64810cc5',
						url: 'git@github.com:transloyd/core.toplyvo.app.git'
					]]
				])
			}
		}
	}
}