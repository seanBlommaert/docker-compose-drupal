
# Get started

## Common apps (mysql, DNS etc)
Start common  apps like mysql and mailhog (you need those first):
`export MYSQL_DATA=/MY/MYSQL/DATA/FOLDER/data; cd /PATH/TO/DOCKER-COMPOSE-DRUPAL/common-apps && docker-compose up -d`
Add this command to your boot-scripts.


This gives you
 - mysql:    1 mysql-container for all persistent databases.
             Available at `localhost:3306` and from your containers at `mysql` .
             Username / password: `root` and `root`.
 - mailhog:  This is a mailsink with web UI where php-containers will send all email to.
             Note that only containers started with the project docker-compose.yml from this repo will use this!
             Available at http://mailhog.localhost:8025/
 - devdns:   This is the central dns-container that provides you the .localhost domain and project hostnames.
             If this is not started, functionality from this repo won't work well (if not at all).


## To get DNS to work on your host / laptop, follow instructions:

## OSX
   Create a file _/etc/resolver/localhost containing_

   _nameserver <listen address of devdns>_
   In OSX, there's a good chance you're using boot2docker, so the listen address will probably be the output of boot2docker ip. Please note that the name of the file created in _/etc/resolver_ has to match the value of the DNS_DOMAIN setting (default "localhost").

### Ubuntu:
   edit _/etc/dhcp/dhclient.conf_
   add:
      supersede domain-name "localhost";
      prepend domain-name-servers 127.0.0.1;
   see: https://github.com/ruudud/devdns


## Run a project
Copy `drupal-project/docker-compose.yml` and `drupal-project/.env` to your project.
Edit `.env` and `docker-compose.yml`.

Run `docker-compose up -d`.

To stop, run `docker-compose down`.

## Fixing Permissions Problems
To avoid potential permissions problems between host machine and containers (e.g. can't access files generated by PHP) you can add a group on your host machine with ID 82, it's a standard GID/UID for www-data user in Alpine Linux.
Read: http://docs.docker4drupal.org/en/latest/permissions/

# Extra info

## PHP logs

Run `docker-compose logs -f php` from your project folder to tail the php logs.

## Drush, composer and drupal console

You might want to add these aliases to your ~/.bashrc file (restart terminal to take effect):

```
# Aliases for docker drush in web/htdocs/docroot folders
alias ddrush='docker-compose exec --user 82 php vendor/bin/drush --root=/var/www/html'
alias ddrupal='docker-compose exec --user 82 php vendor/bin/drupal --root=/var/www/html'
alias webdrush='docker-compose exec --user 82 php vendor/bin/drush --root=/var/www/html/web'
alias webdrupal='docker-compose exec --user 82 php vendor/bin/drupal --root=/var/www/html/web'
alias htdrush='docker-compose exec --user 82 php vendor/bin/drush --root=/var/www/html/htdocs'
alias htdrupal='docker-compose exec --user 82 php vendor/bin/drupal --root=/var/www/html/htdocs'
alias docdrush='docker-compose exec --user 82 php vendor/bin/drush --root=/var/www/html/docroot'
alias docdrupal='docker-compose exec --user 82 php vendor/bin/drupal --root=/var/www/html/docroot'

# Composer in container
alias dcomposer='docker-compose exec --user 82 php composer'

# Login to container
alias dlogin='bash -c "clear && docker-compose exec php sh"'
alias dloginroot='bash -c "clear && docker-compose exec --user 0 php sh"'

# Running web/htdocs/docroot tests
alias dtests='docker-compose exec --user 82 php php core/scripts/run-tests.sh --php /usr/local/bin/php --color --keep-results --concurrency 1  --verbose'
alias dwebtests='docker-compose exec --user 82 php php web/core/scripts/run-tests.sh --php /usr/local/bin/php --color --keep-results --concurrency 1  --verbose'
alias dhttests='docker-compose exec --user 82 php php htdocs/core/scripts/run-tests.sh --php /usr/local/bin/php --color --keep-results --concurrency 1  --verbose'
alias ddoctests='docker-compose exec --user 82 php php htdocs/core/scripts/run-tests.sh --php /usr/local/bin/php --color --keep-results --concurrency 1  --verbose'

# Starting and stopping general containers
alias dstart='export MYSQL_DATA=/MY/MYSQL/DATA/FOLDER/data/docker/mysql; cd /PATH/TO/DOCKER-COMPOSE-DRUPAL/common-apps && docker-compose up -d'
alias dstop='cd /PATH/TO/DOCKER-COMPOSE-DRUPAL/common-apps && docker-compose down'

# Starting and stopping a project
# Note the <E2><80><98>dup' alias adds a workaround to provide working DNS for each php-container.
# Drupal needs this for background_process support etc.
alias dup='export DOCKERDNS=$(docker inspect -f "{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}" dockerdns); docker-compose up -d'
alias ddown='docker-compose down;'
alias drestart='ddown; dup'

# View logs
alias dlogs='docker-compose logs -f php'
```


