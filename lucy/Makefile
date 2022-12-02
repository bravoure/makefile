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
		docker-compose up -d webserver queue redis backend database
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g yarn
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev


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
		docker-compose up -d webserver queue redis backend database
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev

up-production-build:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		@echo "Starting local production build"
		rm -rf frontend/.next
		make composer-install
		docker-compose up -d webserver queue redis backend database
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn build && yarn start

install:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		printf "<?php \n use craft\config\GeneralConfig; \n function localConfig(GeneralConfig \$$config): void \n {\n  \$$config->allowAdminChanges(false); \n }" >backend/config/local_config.php
		yarn install && yarn husky install && cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install
		make composer-install
		docker-compose up -d database
		sleep 10
		docker-compose run --rm queue php craft install/craft --email login@bravoure.nl --password bravoure --interactive=0
#		docker-compose run --rm queue php craft setup/php-session-table
		docker-compose run --rm queue php craft project-seeder-module/seeder/seed-db
		docker-compose run --rm queue php craft resave/entries
		docker-compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE users, userpreferences SET userpreferences.preferences = "{\"language\":\"en-GB\",\"showFieldHandles\":true}" WHERE users.id = userpreferences.userId AND email = "login@bravoure.nl"';
		make down
		docker-compose up -d webserver queue redis backend database
		@echo "\033[0;32m*********************************************************\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m****   \033[0mhttp://localhost:8080 ready for some work!\033[0;32m    ****\033[0m"
		@echo "\033[0;32m****               \033[0mMake something epic!\033[0;32m              ****\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m*********************************************************\033[0m"
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g yarn
		cd frontend && yarn install && npx browserslist@latest --update-db && yarn run dev

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

restart-webserver:
		docker-compose restart webserver

local-config:
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >backend/config/local_config.php

set-login-password:
		docker-compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE users SET password = "$2y$13$qyPE3yKmgnAWAb9sl87tBO.OUNQCJ6FDCEfr1ZuhWoNuCcBPqAQ3C" WHERE email = "login@bravoure.nl"';

set-access-tokens:
		docker-compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE gqltokens SET accessToken = "csAyjOAtaxecSaivPnqR0oY3pBKryErL" WHERE name = "Server to server"';
		docker-compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE gqltokens SET accessToken = "X3B60fZSEBkt2cJqz6McUJwlwUJN4o4K" WHERE name = "Client to server"';

delete-merged-branches:
		git branch --merged| egrep -v "(^\*|master|develop|accept|test|main)" | xargs git branch -d

translate:
		php google-doc-translations.php

fix-frontend:
		cd frontend && yarn install && yarn lint-fix

update-all:
		make update-makefile
		make update-docker-compose
		make update-general
		make update-workflows

update-makefile:
		curl -o Makefile https://raw.githubusercontent.com/bravoure/makefile/main/lucy/Makefile

update-docker-compose:
		curl -o docker-compose.yml https://raw.githubusercontent.com/bravoure/docker-compose/main/lucy/docker-compose.yaml

update-workflows:
		curl -o .github/workflows/deploy-to-test.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/deploy-to-test.yml
		curl -o .github/workflows/feature-draft-pull-request.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/feature-draft-pull-request.yml
		curl -o .github/workflows/format-php.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/format-php.yml
		curl -o .github/workflows/new-hotfix.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/new-hotfix.yml
		curl -o .github/workflows/new-release.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/new-release.yml
		curl -o .github/workflows/phone-home.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/phone-home.yml
		curl -o .github/workflows/publish-new-release.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/publish-new-release.yml
		curl -o .github/workflows/pull-request-check-develop.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/pull-request-check-develop.yml
		curl -o .github/workflows/pull-request-check-main.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/pull-request-check-main.yml
		curl -o .github/workflows/pull-request-check-trigger.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/pull-request-check-trigger.yml
		curl -o .github/workflows/ready-for-review.yml https://raw.githubusercontent.com/bravoure/action-templates/main/templates/ready-for-review.yml

update-general:
		curl -o backend/config/general.php https://raw.githubusercontent.com/bravoure/general-config/main/general.php
		curl -o phpstan.neon https://raw.githubusercontent.com/bravoure/general-config/main/phpstan.neon
