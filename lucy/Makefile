welcome:
		@echo "Hi I'm Lucy. Run \"make up\" to start the project"

up:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		@echo "Starting up project."
		make composer-install
		docker-compose up -d webserver queue redis backend mariadb
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g yarn
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev

up-frontend:
		@echo "Deprecated: please use make up"

up-fast:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "Starting up project. \033[0;31mWARNING, without composer install \033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		docker-compose up -d webserver queue redis backend mariadb
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev

up-stop-local-production-build:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "Starting up project. \033[0;31mWARNING, without composer install \033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		@echo "Starting local production build"
		rm -rf frontend/.next
		make composer-install
		docker-compose up -d webserver queue redis backend mariadb
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn build && yarn start

install:
		@echo "\033[0;31mWARNING, change env.example to new vault. Afterwards delete this line :) \033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		printf "%s\n" "<?php" "	return [" "'allowAdminChanges' => false" "];" >backend/config/local_config.php
		yarn install && yarn husky install && cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install
		make stop
		make composer-install
		docker-compose run --rm queue php craft install/craft --email login@bravoure.nl --language en-GB --password bravoure --site-name "change_to_client_name (EN)" --site-url @siteUrlClientNameEnglish
#		docker-compose run --rm queue php craft setup/php-session-table
		docker-compose run --rm queue php craft project-seeder-module/seeder/seed-db
		docker-compose run --rm queue php craft resave/entries
		make down
		docker-compose up -d webserver queue redis backend mariadb
		@echo "\033[0;32m*********************************************************\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m****   \033[0mhttp://localhost:8080 ready for some work!\033[0;32m    ****\033[0m"
		@echo "\033[0;32m****               \033[0mMake something epic!\033[0;32m              ****\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m*********************************************************\033[0m"

down:
		@echo "Stopping project."
		docker-compose down

stop:
		@echo "Stopping all running containers"
		docker stop $$(docker ps -a -q) || true;

sync:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker-compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker-compose run --rm queue php craft project-config/apply

sync-fast:
		@echo "Syncing project. This can take a minute. \033[0;31mWARNING, without yarn and composer install \033[0m"
		docker-compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker-compose run --rm queue php craft project-config/apply

sync-force:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker-compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker-compose run --rm queue php craft project-config/apply --force

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
