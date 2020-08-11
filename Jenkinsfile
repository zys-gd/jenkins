pipeline {
	agent { node { label 'test-Toplyvo-core-Mayatskiy' } }

	stages {

		stage ('Checkout from git') {
			steps {

//				checkout
//					changelog: false,
//					poll: false,
//					scm: [
//						$class: 'GitSCM',
//						branches: [[name: '*/TPLV-662']],
//						doGenerateSubmoduleConfigurations: false,
//						extensions: [],
//						submoduleCfg: [],
//						userRemoteConfigs: [[
//							credentialsId: '7303d04c-541b-49a3-83f2-834b64810cc5', url: 'git@github.com:transloyd/core.toplyvo.app.git'
//							]]
//						]

				checkout([
					$class: 'GitSCM',
					//branches: [[name: '${ghprbSourceBranch}']],
					branches: [[name: '*/TPLV-662']],
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

		stage ('Setting environment') {
			steps {
				sh "cp .env.dev .env"
				sh "cat .env.docker >> .env"
				sh "cp docker-compose.override.yml.dist docker-compose.override.yml"
			}
		}

		stage ('Run docker-compose') {
			steps {
				sh "sh redeploy.sh"
			}
		}

	}
}