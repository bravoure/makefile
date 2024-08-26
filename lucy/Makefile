welcome:
		@echo "Hi I'm Lucy. Run \"make up\" to start the project"

env-setup:
		@echo "\033[0;32m***************************************************************\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m****        \033[0mMake sure to install the 1password cli\033[0;32m         ****\033[0m"
		@echo "\033[0;32m**** \033[0mhttps://developer.1password.com/docs/cli/get-started/\033[0;32m ****\033[0m"
		@echo "\033[0;32m****                                                       ****\033[0m"
		@echo "\033[0;32m***************************************************************\033[0m"
		cat backend/.env.example | ENV=local op inject -f --out-file backend/.env
		cat frontend/.env.local.example | ENV=local op inject -f --out-file frontend/.env.local
		cat ludwig/.env.example | ENV=local op inject -f --out-file ludwig/.env

freelance-env-setup:
		test -f backend/.env.freelance.example || (printf "# Ask bravoure to get you .env file contents" > backend/.env.freelance.example && echo "\033[0;32mAsk bravoure to get you backend/.env.freelance.example file contents.\033[0m" && false;)
		test -f frontend/.env.local.freelance || (printf "# Ask bravoure to get you .env file contents" > frontend/.env.local.freelance && echo "\033[0;32mAsk bravoure to get you frontend/.env.local.freelance file contents.\033[0m" && false;)
		cp backend/.env.freelance.example backend/.env
		cp frontend/.env.local.freelance frontend/.env.local

up:
		@echo "\033[0;32mStarting up project\033[0m"
		docker compose pull
		make env-setup
		make up-part-2

up-f:
		@echo "\033[0;32mStarting up project (freelancer-mode)\033[0m"
		make freelance-env-setup
		make up-part-2

up-c:
		@if ! grep -q html/backend/web /etc/apache2/sites-available/000-default.conf; then \
			sudo sed -i 's/html/html\/backend\/web/g' /etc/apache2/sites-available/000-default.conf; \
		fi
		@if ! grep -q 8080 /etc/apache2/sites-available/000-default.conf; then \
			sudo sed -i 's/80/8080/g' /etc/apache2/sites-available/000-default.conf; \
		fi
		sudo a2enmod rewrite
		apache2ctl restart
		cd backend && composer install
		cp backend/.env.actions.example backend/.env
		cp frontend/.env.local.actions frontend/.env.local
		printf "<?php \n use craft\config\GeneralConfig; \n function localConfig(GeneralConfig \$$config): void \n {\n  \$$config->allowAdminChanges(false)->devMode(true); \n }" > backend/config/local_config.php
		cd backend && php craft install/craft --email login@bravoure.nl --password bravoure --interactive=0
		cd backend && php craft project-seeder-module/seeder/seed-db
		echo "https://${CODESPACE_NAME}-8080.${GITHUB_CODESPACES_PORT_FORWARDING_DOMAIN}/admin"

up-part-2:
		make composer-install
		docker compose up -d webserver queue redis backend database ludwig
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g npm
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm install && npx browserslist@latest --update-db && npm run dev

up-production-build:
		make env-setup
		@echo "Starting local production build"
		rm -rf frontend/.next
		make composer-install
		docker compose up -d webserver queue redis backend database ludwig
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm install && npx browserslist@latest --update-db && npm build && npm start

install:
		@echo "\033[0;32mInstalling the project.\033[0m"
		make env-setup
		make install-part-2

install-f:
		@echo "\033[0;32mInstalling the project for a freelancer.\033[0m"
		make freelance-env-setup
		make install-part-2

install-part-2:
		printf "<?php \n use craft\config\GeneralConfig; \n function localConfig(GeneralConfig \$$config): void \n {\n  \$$config->allowAdminChanges(false); \n }" > backend/config/local_config.php
		make composer-install
		docker compose up -d database
		sleep 10
		docker compose run --rm queue php craft install/craft --email login@bravoure.nl --password bravoure --interactive=0
#		docker compose run --rm queue php craft setup/php-session-table
		docker compose run --rm queue php craft project-seeder-module/seeder/seed-db
		docker compose run --rm queue php craft resave/entries
		docker compose run --rm database mysql --host=database --user=craft --password=craft --database=craft --port=3306 --execute='UPDATE users, userpreferences SET userpreferences.preferences = "{\"language\":\"en-GB\",\"showFieldHandles\":true}" WHERE users.id = userpreferences.userId AND email = "login@bravoure.nl"';
		make down
		docker compose up -d webserver queue redis backend database ludwig
		docker compose exec ludwig php artisan migrate
		make generate-ide-helper
		@echo "\033[0;32m*********************************************************\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m****   \033[0mhttp://localhost:8080 ready for some work!\033[0;32m    ****\033[0m"
		@echo "\033[0;32m****               \033[0mMake something epic!\033[0;32m              ****\033[0m"
		@echo "\033[0;32m****                                                 ****\033[0m"
		@echo "\033[0;32m*********************************************************\033[0m"
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm i -g npm
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && cd .. && npm install && npm install husky --save-dev && rm -f .husky/pre-commit && npx husky add .husky/pre-commit "cd frontend && . \$${NVM_DIR}/nvm.sh --no-use && nvm install && npx lint-staged"
		cd frontend && . ${NVM_DIR}/nvm.sh --no-use && nvm install && npm install && npx browserslist@latest --update-db && npm run dev

down:
		@echo "Stopping project."
		docker compose down

sync:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker compose run --rm queue php craft project-config/apply
		docker compose run --rm queue-ludwig php artisan migrate
		make graphql-ts-types

sync-fast:
		@echo "Syncing project. This can take a minute. \033[0;31mWARNING, without npm and composer install \033[0m"
		docker compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker compose run --rm queue php craft project-config/apply

sync-force:
		@echo "Syncing project. This can take a minute."
		make composer-install
		docker compose run --rm queue php craft migrate/all --no-backup --interactive=0
		docker compose run --rm queue php craft project-config/apply --force

composer-install:
		docker compose run --rm composer composer install
		docker compose run --rm composer-ludwig composer install
		make generate-ide-helper

composer-dump:
		docker compose run --rm composer composer dump
		docker compose run --rm composer-ludwig composer dump
		make generate-ide-helper

composer-update:
		docker compose run --rm composer composer update

composer-update-ludwig:
		docker compose run --rm composer-ludwig composer update

composer-require:
		@read -p "Enter Package: " package; \
		docker compose run --rm composer composer require $$package

composer-require-ludwig:
		@read -p "Enter Package: " package; \
		docker compose run --rm composer-ludwig composer require $$package
		make generate-ide-helper

composer-remove:
		@read -p "Enter Package: " package; \
		docker compose run --rm composer composer remove $$package

composer-remove-ludwig:
		@read -p "Enter Package: " package; \
		docker compose run --rm composer-ludwig composer remove $$package

apply-code-styles:
		@echo "Applying code styles"
		docker compose run --rm php-cs-fixer
		docker compose run --rm php-cs-fixer-ludwig

logs-backend:
		docker compose logs backend

logs-backend-follow:
		docker compose logs -f backend

logs-webserver:
		docker compose logs webserver

logs-webserver-follow:
		docker compose logs -f webserver

logs-queue:
		docker compose logs queue

logs-queue-follow:
		docker compose logs -f queue

logs-craft:
		docker compose exec backend tail /var/www/backend/storage/logs/web.log

logs-craft-follow:
		docker compose exec backend tail -f /var/www/backend/storage/logs/web.log

restart-webserver:
		docker compose restart webserver

local-config:
		printf "%s\n" "<?php" "	return [" "		'allowAdminChanges' => false" "	];" >backend/config/local_config.php

set-login-password:
		docker compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE users SET password = "$$2y$$13$$qyPE3yKmgnAWAb9sl87tBO.OUNQCJ6FDCEfr1ZuhWoNuCcBPqAQ3C" WHERE email = "login@bravoure.nl"';

set-access-tokens:
		docker compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE gqltokens SET accessToken = "csAyjOAtaxecSaivPnqR0oY3pBKryErL" WHERE name = "Server to server"';
		docker compose run --rm database mysql --host=database --user=craft --password=craft --database=craft  --port=3306 --execute='UPDATE gqltokens SET accessToken = "X3B60fZSEBkt2cJqz6McUJwlwUJN4o4K" WHERE name = "Client to server"';

delete-merged-branches:
		git branch --merged| egrep -v "(^\*|master|develop|accept|test|main)" | xargs git branch -d

translate:
		php google-doc-translations.php
		docker compose run --rm php-cs-fixer

fix-frontend:
		cd frontend && npm install && npm run lint-fix

update-all:
		make update-makefile
		make update-docker-compose
		make update-general
		make update-workflows
		make update-wiki
		make update-webserver

update-wiki:
		git clone git@github.com:bravoure/lucy.wiki.git wiki-tmp
		cd wiki-tmp && git rev-parse HEAD > version.txt
		rm -rf wiki-tmp/.git
		cp -rf wiki-tmp/. wiki
		rm -rf wiki-tmp

update-webserver:
		curl -o config/webserver/local/app.conf https://raw.githubusercontent.com/bravoure/docker-compose/main/lucy/config/webserver/local/app.conf
		curl -o config/webserver/github-actions/app.conf https://raw.githubusercontent.com/bravoure/docker-compose/main/lucy/config/webserver/github-actions/app.conf
update-makefile:
		curl -o Makefile https://raw.githubusercontent.com/bravoure/makefile/main/lucy/Makefile

update-docker-compose:
		curl -o docker compose.yml https://raw.githubusercontent.com/bravoure/docker-compose/main/lucy/docker-compose.yaml

update-workflows:
		curl -o .github/workflows/deploy-to-test.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/deploy-to-test.yml
		curl -o .github/workflows/feature-draft-pull-request.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/feature-draft-pull-request.yml
		curl -o .github/workflows/format-php.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/format-php.yml
		curl -o .github/workflows/format-php-ludwig.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/format-php-ludwig.yml
		curl -o .github/workflows/new-hotfix.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/new-hotfix.yml
		curl -o .github/workflows/new-release.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/new-release.yml
		curl -o .github/workflows/phone-home.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/phone-home.yml
		curl -o .github/workflows/publish-new-release.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/publish-new-release.yml
		curl -o .github/workflows/pull-request-check-develop.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/pull-request-check-develop.yml
		curl -o .github/workflows/pull-request-check-main.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/pull-request-check-main.yml
		curl -o .github/workflows/pull-request-check-trigger.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/pull-request-check-trigger.yml
		curl -o .github/workflows/ready-for-review.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/ready-for-review.yml
		curl -o .github/workflows/create-documentation.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/create-documentation.yml
		curl -o .github/workflows/sync-assets.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/sync-assets.yml
		curl -o .github/workflows/sync-assets-manual.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/sync-assets-manual.yml
		curl -o .github/workflows/react-tests.yml https://raw.githubusercontent.com/bravoure/action-templates/v2/templates/react-tests.yml

update-general:
		curl -o backend/config/general.php https://raw.githubusercontent.com/bravoure/general-config/main/general.php
		curl -o phpstan.neon https://raw.githubusercontent.com/bravoure/general-config/main/phpstan.neon

test-lighthouse:
		mkdir -p lighthouse
		rm -rf lighthouse/mobile
		rm -rf lighthouse/desktop
		mkdir -p lighthouse/mobile
		mkdir -p lighthouse/desktop
		printf "%s\n" "* \n !.gitignore" >lighthouse/.gitignore
		@echo "Enter admin url: (like https://accept-admin.oudekerk.nl NO '/')"
		@read adminUrl; \
		curl -o lighthouse/urls.txt $$adminUrl/api/lighthouse/list
		npm install -g lighthouse
		@echo "\033[0;32m Creating mobile lighthouse tests\033[0m"
		cd lighthouse/mobile && for url in $$(cat ../urls.txt); do lighthouse "$$url" --chrome-flags="--headless" --view --extra-headers="{\"Authorization\":\"Basic YnJhdm91cmU6ZGV2ZWxvcG1lbnQ=\"}"; done
		@echo "\033[0;32m Creating desktop lighthouse tests\033[0m"
		cd lighthouse/mobile && for url in $$(cat ../urls.txt); do lighthouse "$$url" --chrome-flags="--headless" --preset=desktop --view --extra-headers="{\"Authorization\":\"Basic YnJhdm91cmU6ZGV2ZWxvcG1lbnQ=\"}"; done
		rm lighthouse/urls.txt

generate-ide-helper:
		docker compose up -d ludwig
		docker compose exec ludwig php artisan ide-helper:generate
		docker compose exec ludwig php artisan ide-helper:meta

graphql-ts-types:
		docker compose run --rm queue php craft graphql/dump-schema --token csAyjOAtaxecSaivPnqR0oY3pBKryErL
		mv backend/server_to_server.graphql frontend/server_to_server.graphql

index-assets:
		docker compose run --rm queue php craft  index-assets/all
