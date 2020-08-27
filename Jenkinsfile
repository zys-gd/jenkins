// Apply hotfixes from master to stage and develop branches
pipeline {
	agent any

	stages {

		stage ('Checkout master') {

			steps {
				script {
					try {
						checkout([
							$class: 'GitSCM',
							branches: [[name: '*/master']],
							doGenerateSubmoduleConfigurations: false,
							extensions: [[
								$class: 'RelativeTargetDirectory'
							]],
							submoduleCfg: [],
							userRemoteConfigs: [[
								credentialsId: '7303d04c-541b-49a3-83f2-834b64810cc5',
								url: "git@github.com:transloyd/${env.Repository}.git"
							]]
						])

						sh "git branch -v"
					}
					catch (exc) {
						echo exc
						currentBuild.result = 'FAILURE'
						currentStage.result = 'FAILURE'
					}
				}

			}
		}

		stage ('Update stage') {
			steps {

				script {
					try {
						sshagent(['7303d04c-541b-49a3-83f2-834b64810cc5']) {
							sh "git config --global user.email 'develop@toplyvo.app'"
                            sh "git config --global user.name 'Jenkins'"
							sh "git checkout stage --"
							sh "git pull"
							sh "git merge origin/master"
							sh "git push --progress origin stage"
                        }
					}
					catch (exc) {
						echo exc
						sh "git status"
						currentBuild.result = 'FAILURE'
						currentStage.result = 'FAILURE'
					}

				}

			}
		}

		stage ('Update develop') {
            steps {

                script {
                    try {
                        sshagent(['7303d04c-541b-49a3-83f2-834b64810cc5']) {
							sh "git config --global user.email 'develop@toplyvo.app'"
                            sh "git config --global user.name 'Jenkins'"
	                        sh "git checkout develop --"
	                        sh "git pull"
	                        sh "git merge origin/stage"
                            sh "git push --progress origin develop"
                        }
                    }
                    catch (exc) {
                        echo exc
                        sh "git status"
                        currentBuild.result = 'FAILURE'
                        currentStage.result = 'FAILURE'
                    }

                }

            }
        }

	}

}