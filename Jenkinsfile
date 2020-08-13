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
				sh "rm -R *"

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
			environment {
				DOCKER_PREFIX = "${ghprbSourceBranch}"
				CONTAINER_NAME_PREFIX = "${ghprbSourceBranch}"
			}

			steps {
				sh "cd ${ghprbSourceBranch}"
				sh "cp .env.dev .env"
				sh "cat .env.docker >> .env"
				sh "cp docker-compose.jenkins.yml docker-compose.yml"
			}
		}

		stage ('Run docker-compose') {
			steps {
				script {
					try {
						sh '''
							sh "cd ${ghprbSourceBranch}"
							docker-compose stop
							docker-compose rm -f

							docker-compose up -d --build

							docker exec ${ghprbSourceBranch}_toplivo_back_mysql /bin/bash -c "echo \"
								CREATE DATABASE IF NOT EXISTS toplivo_tracking;
								GRANT ALL PRIVILEGES ON toplivo_tracking.* TO 'toplivo_user'
							\" | mysql -uroot -proot123456"

							docker cp ./docker/php/parameters.yml.dist ${ghprbSourceBranch}_toplivo_back_php:/var/www/html/app/config/parameters.yml
							docker exec ${ghprbSourceBranch}_toplivo_back_php /bin/bash -c "cd /var/www/html && composer install"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_php_consumer /bin/bash -c "php bin/console rabbitmq-supervisor:rebuild"
							sleep 5
							docker exec -d ${ghprbSourceBranch}_toplivo_back_php_consumer /bin/bash -c "php bin/console rabbitmq-supervisor:control --wait-for-supervisord start"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_php /bin/bash -c "php bin/console --configuration=./app/config/doctrine/migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_php /bin/bash -c "php bin/console --em=tracking --configuration=./app/config/doctrine/tracking_migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_php_cli /bin/bash -c "/bin/bash /entrypoint.sh"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_nginx /bin/bash -c "chown -R www-data:www-data /var/www/html/var/*"
							docker exec -it --user www-data ${ghprbSourceBranch}_toplivo_back_php /bin/bash -c "php /var/www/html/bin/console assets:install --symlink"
							docker exec -it ${ghprbSourceBranch}_toplivo_back_php bash -c "php vendor/bin/phpcs --config-set installed_paths vendor/escapestudios/symfony2-coding-standard/Symfony"
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