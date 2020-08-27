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
								url: 'git@github.com:transloyd/Jenkins_Toplyvo_UA.git'
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
						sh "git checkout stage --"
						sh "git merge master"
						sh "git push --progress origin origin/stage"
					}
					catch (exc) {
						echo exc
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
                        sh "git checkout develop --"
                        sh "git merge stage"
                        sh "git push --progress origin origin/develop"
                    }
                    catch (exc) {
                        echo exc
                        currentBuild.result = 'FAILURE'
                        currentStage.result = 'FAILURE'
                    }

                }

            }
        }

	}

}