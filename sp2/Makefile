welcome:
		@echo "Hi I'm SP2. Run \"make up\" to start the project"

up:
		@echo "\033[0;32mStarting up project\033[0m"
		make env-setup
		make up-part-2

install:
		@echo "\033[0;32mInstalling the project.\033[0m"
		make env-setup
		make install-part-2

env-setup:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat .env.example | ENV=local op inject -f --out-file .env

up-part-2:
		make composer-install
		docker-compose up -d webserver queue redis backend database
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g yarn
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev

install-part-2:
		printf "<?php \n use craft\config\GeneralConfig; \n function localConfig(GeneralConfig \$$config): void \n {\n  \$$config->allowAdminChanges(false); \n }" > config/local_config.php
		make composer-install
		docker-compose up -d database
		sleep 10
		docker-compose run --rm queue php craft install/craft --email login@bravoure.nl --password bravoure --interactive=0
#		docker-compose run --rm queue php craft setup/php-session-table
		docker-compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE users, userpreferences SET userpreferences.preferences = "{\"language\":\"en-GB\",\"showFieldHandles\":true}" WHERE users.id = userpreferences.userId AND email = "login@bravoure.nl"';
		make down
		docker-compose up -d webserver queue redis backend database
		@echo "\033[0;32m*********************************************************\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m****   \033[0mhttp://localhost:8080 ready for some work!\033[0;32m    ****\033[0m"
		@echo "\033[0;32m****               \033[0mMake something epic!\033[0;32m              ****\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m*********************************************************\033[0m"

down:
		@echo "Stopping project."
		docker-compose down

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
		cat backend/storage/logs/web.log

restart-frontend:
		docker-compose restart frontend

restart-webserver:
		docker-compose restart webserver

local-config:
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >config/local_config.php

update-makefile:
		curl -o Makefile https://raw.githubusercontent.com/bravoure/makefile/main/sp2/Makefile
