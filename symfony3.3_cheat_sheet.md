# symfony 3.3 cheat sheet

## Installation
```
sudo curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony
sudo chmod a+x /usr/local/bin/symfony
```
[more](https://symfony.com/doc/current/setup.html)

## Create project
```
cd /var/www/html
symfony new project_name
```
## Access project (built-in web server)
```
cd /var/www/html/project_name
php bin/console server:start
```
### Virtual machines
```
cd /var/www/html/project_name
php bin/console server:start 0.0.0.0:8000
```
### Stop server
```
php bin/console server:stop
```