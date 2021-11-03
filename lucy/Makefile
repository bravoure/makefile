welcome:
		@echo "Hi I'm Lucy. Run \"make up\" to start the project"

up:
		@echo "Starting up project."
		make composer-install
		@echo "party is on"
		docker-compose up --scale composer=0 --scale php-cs-fixer=0 --remove-orphans

up-frontend:
		@echo "Starting up project."
		docker-compose up --scale composer=0 --scale php-cs-fixer=0 --scale mariadbdzbl=0 --scale mariadb=0 --scale queue=0 --scale redis=0

up-fast:
		@echo "Starting up project. \033[0;31mWARNING, without composer install \033[0m"
		docker-compose up --scale composer=0 --scale php-cs-fixer=0

up-max:
		@echo "Starting up project. \033[0;31mWARNING, without composer install \033[0m"
		docker-compose up --scale composer=0 --scale queue=2 --scale backend=1 --scale php-cs-fixer=0

up-stop:
		@echo "Stopping al containers and starting up project."
		make stop
		make composer-install
		docker-compose up webserver

up-stop-local-production-build:
		@echo "Stopping al containers and starting up project."
		make stop
		rm -rf frontend/.next
		make composer-install
		docker-compose up -d webserver
		docker-compose run --rm frontend sh -c 'yarn install && yarn build && yarn start'

up-fe-logs:
		@echo "Starting up project."
		make composer-install
		docker-compose up --scale composer=0 --scale php-cs-fixer=0 -d
		docker-compose logs -f frontend

install:
		cp backend/.env.docker.example backend/.env
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >backend/config/local_config.php
		make stop
		make composer-install
		docker-compose run queue ./craft install/craft
		docker-compose down
		docker-compose up webserver

down:
		@echo "Stopping project."
		docker-compose down

stop:
		@echo "Stopping all running containers"
		docker stop $$(docker ps -a -q);

sync:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker-compose run --rm frontend yarn install
		docker-compose run --rm queue ./craft migrate/all --no-backup --interactive=0
		docker-compose run --rm queue ./craft project-config/apply

sync-fast:
		@echo "Syncing project. This can take a minute. \033[0;31mWARNING, without yarn and composer install \033[0m"
		docker-compose run --rm queue ./craft migrate/all
		docker-compose run --rm queue ./craft project-config/apply

sync-force:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker-compose run --rm frontend yarn install
		docker-compose run --rm queue ./craft migrate/all --no-backup --interactive=0
		docker-compose run --rm queue ./craft project-config/apply --force

build:
		docker-compose build

yarn-add:
		@read -p "Enter Package: " package; \
		docker-compose exec frontend yarn add $$package

composer-dump:
		docker-compose run --rm composer composer dump

composer-update:
		docker-compose run --rm composer composer update

composer-require:
		@read -p "Enter Package: " package; \
		docker-compose run --rm composer composer require $$package

composer-remove:
		@read -p "Enter Package: " package; \
		docker-compose run --rm composer composer remove $$package

composer-install:
		docker-compose run --rm composer composer install

apply-code-styles:
		@echo "Applying code styles"
		docker-compose run --rm php-cs-fixer

logs-frontend:
		docker-compose logs frontend

logs-frontend-follow:
		docker-compose logs -f frontend

logs-backend:
		docker-compose logs backend

logs-backend-follow:
		docker-compose logs -f backend

logs-webserver:
		docker-compose logs webserver

logs-webserver-follow:
		docker-compose logs -f webserver

logs-queue:
		docker-compose logs queue

logs-queue-follow:
		docker-compose logs -f queue

logs-craft:
		docker-compose exec backend tail /var/www/backend/storage/logs/web.log

logs-craft-follow:
		docker-compose exec backend tail -f /var/www/backend/storage/logs/web.log

logs-feedme:
		docker-compose exec backend tail /var/www/backend/storage/logs/feedme.log

logs-feedme-follow:
		docker-compose exec backend tail -f /var/www/backend/storage/logs/feedme.log

restart-frontend:
		docker-compose restart frontend

restart-webserver:
		docker-compose restart webserver

local-config:
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >backend/config/local_config.php

set-login-password:
		mysql --host=127.0.01 --user=craft --password=craft --database=craft  --port=3307 --execute='UPDATE users SET password = "$2y$13$qyPE3yKmgnAWAb9sl87tBO.OUNQCJ6FDCEfr1ZuhWoNuCcBPqAQ3C" WHERE email = "login@bravoure.nl"';

delete-merged-branches:
	git branch --merged| egrep -v "(^\*|master|develop|accept|test)" | xargs git branch -d

update-makefile:
		curl -o Makefile https://raw.githubusercontent.com/bravoure/makefile/master/lucy/Makefile
