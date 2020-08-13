pipeline {
	agent { node { label 'test-Toplyvo-core-Mayatskiy' } }

	stages {

		stage ('Checkout from git') {
			steps {



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
				DOCKER_PREFIX="${ghprbSourceBranch}"
				CONTAINER_NAME_PREFIX="${ghprbSourceBranch}"
				COMPOSE_INTERACTIVE_NO_CLI=1
				REDIS_HOST="${ghprbSourceBranch}_toplivo_back_redis"
				MYSQL_DATABASE_HOST="${ghprbSourceBranch}_toplivo_back_mysql"
			}
			steps {
				dir("${ghprbSourceBranch}") {
					script {
						try {
							sh '''
								docker-compose stop
								docker-compose rm -f

								docker-compose up -d --build

								docker-compose exec -T php composer install
								docker-compose exec -T php_consumer php bin/console rabbitmq-supervisor:rebuild
								sleep 5
								docker-compose exec -T php_consumer php bin/console rabbitmq-supervisor:control --wait-for-supervisord start

								docker-compose exec -T php php bin/console --configuration=./app/config/doctrine/migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug

								ocker-compose exec -T php php bin/console doctrine:database:create --connection=tracking
								docker-compose exec -T php php bin/console --em=tracking --configuration=./app/config/doctrine/tracking_migrations.yml doctrine:migrations:migrate --allow-no-migration --no-interaction --no-debug

								docker-compose exec -T php_cli bash /entrypoint.sh
								docker-compose exec -T nginx chown -R www-data:www-data /var/www/html/var/
								docker-compose exec -T php php /var/www/html/bin/console assets:install --symlink
								docker-compose exec -T php php vendor/bin/phpcs --config-set installed_paths vendor/escapestudios/symfony2-coding-standard/Symfony
							'''
						}
						catch (exc) {
							sh "docker-compose down"
							currentBuild.result = 'FAILURE'
							currentStage.result = 'FAILURE'
						}
					}
				}
			}
		}

	}
}