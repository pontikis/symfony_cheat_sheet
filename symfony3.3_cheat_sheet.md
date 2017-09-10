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

(using of ``0.0.0.0`` is convinient, but NOT safe in real networks)

### stop server
```
php bin/console server:stop
```
### remarks
* Allow port 8000 if you use firewall. Foe example (in case of iptables): ``iptables -A INPUT -p tcp --dport 8000 -j``
* Php built-in web server doesn't support SSL encryption. It's for plain HTTP requests. 