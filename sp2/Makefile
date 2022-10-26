welcome:
		@echo "Hi I'm SP2. Run \"make up\" to start the project"

up:
		@echo "Starting up project."
		docker-compose up -d backend
		docker-compose exec backend composer install
		docker-compose up

up-stop:
		@echo "Stopping al containts and starting up project."
		docker stop $$(docker ps -a -q);
		docker-compose up -d backend
		docker-compose exec backend composer install
		docker-compose up

install:
		cp .env.docker.example .env
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >config/local_config.php
		docker stop $$(docker ps -a -q);
		docker-compose up -d
		docker-compose exec backend composer install
		docker-compose exec backend ./craft install/craft
		docker-compose down
		docker-compose up

up-fe-logs:
		@echo "Starting up project."
		docker-compose up -d backend
		docker-compose exec backend composer install
		docker-compose up -d
		docker-compose logs -f frontend

down:
		@echo "Stopping project."
		docker-compose down

stop:
		@echo "Stopping all running containers"
		docker stop $(docker ps -a -q)

sync:
		@echo "Syncing project. This can take a minute."
		docker-compose exec backend composer install
		docker-compose exec frontend yarn install
		docker-compose exec backend ./craft migrate/all
		docker-compose exec backend ./craft project-config/apply

sync-force:
		@echo "Syncing project. This can take a minute."
		docker-compose exec backend composer install
		docker-compose exec frontend yarn install
		docker-compose exec backend ./craft migrate/all
		docker-compose exec backend ./craft project-config/apply --force

build:
		docker-compose build

yarn-add:
		@read -p "Enter Package: " package; \
		docker-compose exec frontend yarn add $$package

composer-dump:
		docker-compose exec backend composer dump

composer-update:
		docker-compose exec backend composer update

composer-require:
		@read -p "Enter Package: " package; \
		docker-compose exec backend composer require $$package

composer-remove:
		@read -p "Enter Package: " package; \
		docker-compose exec backend composer remove $$package

composer-install:
		docker-compose exec backend composer install

apply-code-styles:
		@echo "Applying code styles"
		docker-compose exec backend php config/code-style/php-cs-fixer-v2.phar fix

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
		cat backend/storage/logs/web.log

restart-frontend:
		docker-compose restart frontend

restart-webserver:
		docker-compose restart webserver

local-config:
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >backend/config/local_config.php
		
delete-merged-branches:
		git branch --merged| egrep -v "(^\*|master|develop|accept|test)" | xargs git branch -d

update-makefile:
		curl -o Makefile https://bitbucket.org/bravoure/makefile-sp2/raw/HEAD/Makefile
