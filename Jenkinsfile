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
//				script {
//					try {
//						dir("${ghprbSourceBranch}") {
//							deleteDir()
//						}
//					} catch (err) {
//						echo "Dir doesnt exist. Continue."
//					}
//				}


				checkout([
					$class: 'GitSCM',
					//branches: [[name: '${ghprbSourceBranch}']],
					branches: [[name: '*/TPLV-662']],
					doGenerateSubmoduleConfigurations: false,
					extensions: [[
						$class: 'RelativeTargetDirectory',
						relativeTargetDir: '${ghprbSourceBranch}'
					]],
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
				dir("${ghprbSourceBranch}") {
					sh "cp .env.dev .env"
					sh "cat .env.docker >> .env"
					sh "cp docker-compose.jenkins.yml docker-compose.yml"
				}
			}
		}

		stage ('Run docker-compose') {
			environment {
				DOCKER_PREFIX = "${ghprbSourceBranch}"
				CONTAINER_NAME_PREFIX = "${ghprbSourceBranch}"
			}
			steps {
				dir("${ghprbSourceBranch}") {
					script {
						try {
							sh '''
								docker-compose stop
								docker-compose rm -f

								docker-compose up -d --build

								docker cp ./docker/php/parameters.yml.dist php:/var/www/html/app/config/parameters.yml
								docker-compose exec php /bin/bash -c "cd /var/www/html && composer install"
								docker-compose exec php_consumer /bin/bash -c "php bin/console rabbitmq-supervisor:rebuild"
								sleep 5
								docker-compose exec -d php_consumer /bin/bash -c "php bin/console rabbitmq-supervisor:control --wait-for-supervisord start"
								docker-compose exec php /bin/bash -c "php bin/console --configuration=./app/config/doctrine/migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug"
								docker-compose exec php /bin/bash -c "php bin/console --em=tracking --configuration=./app/config/doctrine/tracking_migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug"
								docker-compose exec php_cli /bin/bash -c "/bin/bash /entrypoint.sh"
								docker-compose exec nginx /bin/bash -c "chown -R www-data:www-data /var/www/html/var/*"
								docker-compose exec --user www-data php /bin/bash -c "php /var/www/html/bin/console assets:install --symlink"
								docker-compose exec php bash -c "php vendor/bin/phpcs --config-set installed_paths vendor/escapestudios/symfony2-coding-standard/Symfony"
							'''
						}
						catch (exc) {
							sh "docker-compose down"
						}
					}
				}
			}
		}

	}
}