---
layout: archive
title:  "CentOS 7 Minimal Nginx, Php, MariaDB Install"
date:   2019-05-14 14:00:00 +0900
---

CentOS 7
------------------------

First, .bash_profile modify
```
export PS1="\[\e[36;1m\]\u@\[\e[32;1m\]\h:\[\e[31;1m\]\w:> \[\e[0m\]"
```

Second, yum update -y
```
yum update -y
```

Third, reboot system
```
reboot
```


Nginx
------------------------
Download Nginx signing key.
```
wget http://nginx.org/keys/nginx_signing.key
```

Import it the the rpm keyring.
```
rpm --import nginx_signing.key
```

Create a repo file under /etc/yum.repo.d/ directory.
```
nano /etc/yum.repos.d/nginx.repo

OR

vi /etc/yum.repos.d/nginx.repo
```

Paster the following lines to this file.
```
[nginx.org]
name=nginx.org repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=1
enabled=1
```

Install Nginx
```
yum install nginx -y
```

Start Nginx Web server and enable autostart on system boot with systemctl.
```
systemctl start nginx

systemctl enable nginx
```


Php
------------------------
Add PHP Remi repository
```
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm -y
yum install epel-release yum-utils -y
```

By default, the enabled repository is for PHP 5.4. Disable this repo and enable on for PHP 7.X
```
yum-config-manager --disable remi-php54

yum-config-manager --enable remi-php70
yum-config-manager --enable remi-php71
yum-config-manager --enable remi-php72
yum-config-manager --enable remi-php73
```

Install redis, memcached

```
yum install redis memcached -y
```

Install Php

```
yum install php-fpm php-cli php-redis php-brotli php-gd php-gmp php-imap php-bcmath php-interbase php-json php-mbstring php-mysqlnd php-odbc php-opcache php-memcached php-tidy php-pdo php-pdo-dblib php-pear php-pgsql php-process php-pecl-apcu php-pecl-geoip php-pecl-gmagick php-pecl-hrtime php-pecl-json php-pecl-memcache php-pecl-mongodb php-pecl-rar php-pecl-pq php-pecl-redis4 php-pecl-yaml php-pecl-zip -y
```

Start php, redis, memcached and enable autostart on system boot with systemctl.
```
systemctl enable php-fpm
systemctl enable redis
systemctl enable memcached
systemctl start redis
systemctl start memcached
systemctl start php-fpm
```

Modify memcached (memcached listen only localhost)
```
nano /etc/sysconfig/memcached

OR

vi /etc/sysconfig/memcached
```

```
PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"
OPTIONS="-l 127.0.0.1"
```

```
systemctl restart memcached
```

```
yum install net-tools -y
```

```
netstat -an | grep ":11211"
tcp        0      0 127.0.0.1:11211         0.0.0.0:*               LISTEN
udp        0      0 127.0.0.1:11211         0.0.0.0:*
```

/var/lib/php/ directory modify (Initialize to apache group each time package is updated)
```
/var/lib/php/opcache
/var/lib/php/session
/var/lib/php/wsdlcache
```

Make directory
```
/var/lib/php-save/opcache
/var/lib/php-save/session
/var/lib/php-save/wsdlcache
```

```
chown -R root:nginx /var/lib/php-save
chmod -R 775 /var/lib/php-save
```


```
nano /etc/php-fpm.d/www.conf

OR

vi /etc/php-fpm.d/www.conf
```

```
php_value[session.save_path]    = /var/lib/php-save/session
php_value[soap.wsdl_cache_dir]  = /var/lib/php-save/wsdlcache
php_value[opcache.file_cache]  = /var/lib/php-save/opcache
```

MariaDB
------------------------
MariaDB Package Repository Setup Script
```
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
```

Install MariaDB
```
yum install MariaDB-server MariaDB-client -y
```

Start MariaDB and enable autostart on system boot with systemctl.
```
systemctl enable mariadb
systemctl start mariadb
```

Run the mysql_secure_installation script which will perform several security related tasks:
```
$ mysql_secure_installation
```