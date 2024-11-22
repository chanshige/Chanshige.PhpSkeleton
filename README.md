PHP standard project
--
nginx - php-fpm(Fast CGI) 

directory structure
--
    .
    ├── README.md [This file]
    ├── compose.yml
    ├── docker/  # infra
    ├── misc     # Zatta
    ├── qatools/ # QA.Tools
    └── www/     # application

version
--
- linux alpine 3.20
- nginx 1.x
- php 8.3.x
- postgresql 15.x

create project
--
### create self-signed certificate
    $ cd ./docker/development/nginx/ssl
    $ openssl genrsa 4096 > server.key

    $ openssl req -new -key server.key > server.csr
        Country Name (2 letter code) []:JP
        State or Province Name (full name) []:Fukuoka
        Locality Name (eg, city) []:Fukuoka
        Organization Name (eg, company) []:CHANSHIGE.inc
        Organizational Unit Name (eg, section) []:
        Common Name (eg, fully qualified host name) []:localhost
        Email Address []:

        $ openssl x509 -days 3650 -req -sha256 -signkey server.key < server.csr > server.crt

### installation
    > cd ./
    $ cp .env.compose .env
    $ docker-compose build php --no-cache
    $ docker compose run --rm --no-deps php composer create-project symfony/skeleton .
    $ docker-compose up -d nginx

qatools
--
Included in this package are:  

* [phpunit/phpunit](https://github.com/sebastianbergmann/phpunit) The PHP Unit Testing framework.
* [phploc/phploc](https://github.com/sebastianbergmann/phploc) A tool for quickly measuring the size of a PHP project.
* [phpmd/phpmd](https://github.com/phpmd/phpmd) PHPMD is a spin-off project of PHP Depend and aims to be a PHP equivalent of the well known Java tool PMD.
* [squizlabs/php_codesniffer](https://github.com/squizlabs/PHP_CodeSniffer) PHP_CodeSniffer tokenises PHP, JavaScript and CSS files and detects violations of a defined set of coding standards.
* [phpstan/phpstan](https://github.com/phpstan/phpstan) A PHP Static Analysis Tool.
* [phpmetrics/phpmetrics](http://www.phpmetrics.org/) Static analysis tool for PHP.

### installation
    // Add "require-dev" and "scripts" in ./qatools/composer.json to ./www/composer.json

    $ cp ./qatools/phpcs.xml ./www
    $ cp ./qatools/phpmd.xml ./www
    $ cp ./qatools/phpmetrics.json ./www
    $ cp ./qatools/phpstan.neon ./www
    $ cp ./qatools/phpunit.xml.dist ./www/phpunit.xml
    $ rm -rf ./qatools
