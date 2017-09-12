# symfony 3.3 cheat sheet

[Symfony](https://symfony.com) cheat sheet for Linux. 

Version 0.2.3 (see CHANGELOG.md for details).

Various commands are tested in [Debian](https://www.debian.org/) server (Jessie with php 5.7 or Stretch with php 7). [Php Built-in web server](http://php.net/manual/en/features.commandline.webserver.php) is used on development. [Apache](https://httpd.apache.org/) with `mod_php` is used on production. Some commands may not work if your system setup is different. Moreover in MacOS or Windows systems. In these cases a link [more](https://symfony.com/doc) is set to [Symfony doc](https://symfony.com/doc).

Most commands executed as normal user. Where root privileges are required, it is mentioned accordingly. 

Copyright Christos Pontikis http://www.pontikis.net

Project page https://github.com/pontikis/symfony_cheat_sheet

License [MIT](https://github.com/pontikis/symfony_cheat_sheet/blob/master/LICENSE)

## Installation
As root (otherwise use `sudo`)
```
curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony
chmod a+x /usr/local/bin/symfony
```
[more](https://symfony.com/doc/current/setup.html)

## Create project
```
cd /var/www/html
symfony new project_name
```
## Launch project for development (built-in web server)

### localhost
```
cd /var/www/html/project_name
php bin/console server:start
```
Point your browser to http://localhost:8000

### outside localhost
Suppose 78.47.62.248 is the server IP
```
cd /var/www/html/project_name
php bin/console server:start 78.47.62.248:8000
```
Point your browser to http://78.47.62.248:8000

### virtual machines
Suppose 192.168.1.103 is the (virtual) server IP
```
cd /var/www/html/project_name
php bin/console server:start 0.0.0.0:8000
```
Point your browser to http://192.168.1.103:8000

(using of `0.0.0.0` is convenient, but **NOT safe** in real networks)

### stop server
```
php bin/console server:stop
```
### remarks
* Allow port 8000 if you use firewall. For example (in case of iptables): `iptables -A INPUT -p tcp --dport 8000 -j` (as root)
* Php built-in web server doesn't support SSL encryption. It's for plain HTTP requests. 

## Launch project on production (Apache 2.4 with mod_php)

### file permissions (important)
As root:
```
cd /var/www/html/project_name
setfacl -dR -m u:www-data:rwX var
setfacl -R -m u:www-data:rwX var
```
[more](http://symfony.com/doc/current/setup/file_permissions.html)

### Apache configuration
As root:
```
cd /etc/apache2/sites-available/
nano www.project-name.com.conf
```
the file looks like:
```
<VirtualHost 78.47.62.248:80>
    ServerName project-name.com
    ServerAlias www.project-name.com
    ServerAdmin you@your-email.com
    
    DocumentRoot /var/www/html/project_name/web
    
    <Directory /var/www/html/project_name/web>
        Options -Indexes +FollowSymLinks +MultiViews
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/project-name.com_error.log
    
    # Possible values include: debug, info, notice, warn, error, crit,
    # alert, emerg.
    LogLevel warn
    
    CustomLog ${APACHE_LOG_DIR}/project-name.com_access.log combined

</VirtualHost>
```
enable site:
```
a2ensite www.project-name.com.conf
```
restart Apache:
```
systemctl restart apache2.service
```
Point your browser to http://www.project-name.com

[more](https://symfony.com/doc/current/setup/web_server_configuration.html)

## Symfony with Git

After creating Symfony project, create Git repository:
```
cd /var/www/html/project_name
git init
git add .
git commit -m "Initial commit"
```
It is strongly recommended to use a remote hosting service like [GitHub](https://github.com) or [Bitbucket](https://bitbucket.org/).

Create a Git repo in Github https://github.com/new Something like https://github.com/username/project_name
```
cd /var/www/html/project_name
git remote add github git@github.com:username/project_name.git 
git push github master
```
So, you (or your partners) can checkout Symfony project from Github  
```
cd /var/www/html
git clone git@github.com:username/project_name.git 
```
It is important to know the `app/config/parameters.yml` and `vendor` directory **are not included** in Git repo. To create them:
```
cd /var/www/html/project_name
composer install
```
## Composer basics

[Composer](https://getcomposer.org/) is a dependency manager for PHP.

Packages are available in [Packagist](https://packagist.org)

### install Composer

In Debian Stretch as root: `apt-get install composer` It installs v1.2.2-1

In Debian Jessie or earlier, use the [universal installer](https://getcomposer.org/download/)

Go to `/tmp`
```
cd /tmp
```
Then, as root:
```php
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php --install-dir=/usr/local/bin --filename=composer
php -r "unlink('composer-setup.php');"
```
(the Installer Signature (SHA-384) may change from version to version, so get it from above page or [here](https://composer.github.io/pubkeys.html))

### add package
```
composer require vendor-name/package-name
```
It will install new package and add it to `composer.json` file in the current directory.

### install packages
```
composer install
```
It will read the `composer.json` from the current directory, resolve the dependencies, and install them to folder `vendor`. It will also create `composer.lock` if not existed.

### update packages
```
composer update
```
It will update dependencies to the latest version (and `composer.lock` file, as well).

## PhpStorm

If you are using [Phpstorm](https://www.jetbrains.com/phpstorm), remember to install 
* [Symfony plugin](https://plugins.jetbrains.com/plugin/7219-symfony-plugin)
* [Php Annotations plugin](https://plugins.jetbrains.com/plugin/7320-php-annotations)

[Composer](https://getcomposer.org/) can be used from PhpStorm to install or update dependencies. Minimum requirements:
* composer 
* php-cli (for Debian/Ubuntu `apt-get install php-cli`)
* php-xml (for Debian/Ubuntu `apt-get install php-xml`)

## Directory structure

The default and recommended Symfony project structure is as follows:

Dir         | Subdir                       | Description
----------- | ---------------------------- | ----------------------------------------
**app/**    | `app/config`                 | application configuration
.           | `app/Resources/views`        | the [Twig](https://twig.symfony.com/) templates
.           | `app/Resources/translations` | the translations 
.           |                              | the `AppKernel` class (`AppKernel.php`) which is the main entry point of the application where Symfony Bundles are registered
bin/        |                              |  Executable files (e.g. `bin/console`)
**src/**    | `src/AppBundle`              | The project's PHP code (Rooting and Controllers)
tests/      |                              | Automatic tests (e.g. Unit tests)
var/        |                              | Generated files (cache, logs, etc.)
vendor/     |                              | The third-party dependencies managed by Composer (php Classes etc)
web/        |                              | The public web root directory with the Front Controller (`app.php` or `app_dev.php`) and the public web assets (CSS, JS, images etc)

> In Symfony speak, a bundle is a structured set of files (PHP files, stylesheets, JavaScripts, images, ...) that implements a single feature (a blog, a forum, ...) and which can be easily shared with other developers.

The most "important" folders during development are **src/** (PHP files) and **app/** (everything else).