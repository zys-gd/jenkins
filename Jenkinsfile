echo currentBuild.rawBuild.getCause(Cause.UserIdCause).getUserId()
def specificCause = currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')
def user = specificCause ? specificCause.userName : '-'

pipeline {
	agent { node { label 'master' } }
	options {
		skipDefaultCheckout true
	}
	stages {
		stage ('Get build configuration') {
			steps {
				script {
				    try {
					checkout([
						$class: 'GitSCM',
						branches: [[name: '*/stage']],
						doGenerateSubmoduleConfigurations: false,
						extensions: [],
						submoduleCfg: [],
						userRemoteConfigs: [[
							credentialsId: '7303d04c-541b-49a3-83f2-834b64810cc5',
							url: 'git@github.com:transloyd/core.toplyvo.app.git'
						]]
					])
				} catch (exc) {}}
			}
		}

		stage('Compare branches') {
			steps {
				script {
					def logs = sh (
						script: "git log origin/stage...origin/develop | grep -e '[A-Z]\\+-[0-9]\\+' -o | sort | uniq | awk -F, '{print \$0}' ",
						returnStdout: true
					).split( '\n' )
					logs.each {value ->
                        echo value
                    }
				}
			}
		}
		
	}
}
