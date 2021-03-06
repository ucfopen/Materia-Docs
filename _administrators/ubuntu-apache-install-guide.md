---
title: Server Setup - Ubuntu and Apache
tagline: How to set up a LAMP server for hosting Materia in Production
class: admin
category: [server, server_setup]
---

# Installing Materia on Ubuntu using Apache

This guide was created for an older version of Materia. It's here as an example, but we suggest using the current release version, which is likely to have updated requirements.

The dependencies to run Materia are included in the Dockerfiles we use for local development. Those can be found in the Materia repository here: [PHP webserver dockerfile](https://github.com/ucfopen/Materia/blob/master/docker/dockerfiles/materia-web) and [node dockerfile](https://github.com/ucfopen/Materia/blob/master/docker/dockerfiles/materia-node). Note that you don't need 2 servers, we just use 2 containers to isolate responsibilities.

## Versions used in this Guide

* Created August 2017
* Materia version: **v3.5.2**
* Ubuntu version: **16.04.2 LTS**
* MySQL version: **5.7.18**
* PHP version: **7.0.18**

## Update Package List

Make sure apt-get's repositories are up to date.

```shell
$ sudo apt-get update
```

## Install MySQL Server

Install `mysql` and run its secure setup.  In many production configurations, the MySQL server will not be on the same server, so you may be able to skip these steps.

```shell
$ sudo apt-get install -y mysql-server
$ sudo service mysql start
$ mysql_secure_installation
```

Make sure the MySQL server starts on boot.

```shell
$ sudo update-rc.d mysql defaults
```

## Materia Database and User

Log into the MySQL database with your root user.

```shell
$ mysql -h localhost -u root -p
Welcome to the MySQL monitor.  Commands end with ; or \g.
...
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Create a `materia` database.

```shell
mysql> create database materia;
Query OK, 1 row affected (0.00 sec)
```

Create a `materia` user. Make sure to create a secure password for it.

```shell
mysql> create user 'materia' identified by '<YOUR_MATERIA_USER_PASSWORD>';
Query OK, 1 row affected (0.00 sec)
```

Grant the `materia` user access to the `materia` database.

```shell
mysql> grant all privileges on materia.* to 'materia';
Query OK, 1 row affected (0.00 sec)

mysql> exit
```

Test your new user. This time use your `materia` user's password.

```shell
$ mysql -h localhost -u materia -p
```

And let's check to see if the `materia` user can read the database.

```shell
mysql> use materia;
Database changed

mysql> show tables;
Empty set (0.00 sec)

mysql> exit
```

Keep your chosen username, database name, and password for later.

## Install Apache Server

Install and start the server.

```shell
$ sudo apt-get install -y \
apache2 \
libapache2-mod-xsendfile \
libapache2-mod-php

$ sudo service apache2 start
```

At this point you should be able to see the default Apache page in your browser.

Make sure Apache starts on boot

```shell
$ sudo update-rc.d apache2 defaults
```

## Install Memcached

Memcached is *not required* but **highly recommended** for production use.

```shell
$ sudo apt-get install -y memcached
$ sudo service memcached start
```

Make sure Memcached starts on boot.

```shell
$ sudo update-rc.d memcached defaults
```

## Install PHP and Extensions

Run the following multi-line command.

```shell
$ sudo apt-get install -y \
php7.0 \
php7.0-gd \
php7.0-bcmath \
php7.0-mbstring \
php7.0-mcrypt \
php7.0-mysql \
php7.0-xml \
php7.0-zip \
php7.0-opcache \
php-memcached \
composer
```

## System Locales

Materia relies on your system's locales, make sure you have the locales available.

List all the locales on your system.

```shell
$ locale -a
```

If `en_US` isn't listed, install it.

```shell
$ sudo apt-get install -y locales
$ sudo locale-gen en_US en_US.UTF-8
```


## Install Node and NPM

Node 6.x is needed to compile Materia's Javascript Assets.

```shell
$ sudo apt-get install curl
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```

## Install Git

Git is needed to clone Materia from source and to access various dependencies.

We'll also set up a credential helper to make things easier.

```shell
$ sudo apt-get install -y git
$ git config --global credential.helper cache
$ git config --global credential.helper 'cache --timeout=3600'
```

## Install Materia

Use Git to clone Materia into a directory. We'll use `/var/www/materia`.

```shell
$ git clone --branch v3.5.2 https://github.com/ucfopen/Materia.git /var/www/materia
```

Next we'll install Materia's Javascript dependencies using NPM.

```shell
# in /var/www/materia
$ npm install --only=production
```

>  If npm can't access materia-client-assets, your git credential cache probably timed out. Running step 1 below should should fix it.
>
> 1. in `/var/www/materia` run `git fetch`
> 2. Run `npm install --only=production` again

Compile Javascript and CSS.

```shell
# in /var/www/materia
$ npm run postinstall
```

Install Materia's PHP dependencies with Composer.

```shell
# in /var/www/materia
$ composer install --no-dev --no-scripts --optimize-autoloader
```


## Materia Configuration

Run Materia's setup Wizard.

```bash
# in /var/www/materia
# When prompted, choose 'production' and run the wizard
$ php oil r install
```
> Enter `y` for all `y, n` questions.
>
> For the most part you can use the default settings for all options, the non-default options are listed below.
>
> Note: It's safe to use the secrets and keys generated by the wizard, they're unique and sufficiently random to be secure.
>
> * Environment: `production`
> * Apache X-Sendfile: `true`
> * Cache Driver: `memcached`
> * Session Driver: `memcached`
> * Database user password: `<YOUR_MATERIA_USER_PASSWORD>` from the mysql setup steps above
> * Unique LTI Identifier: Set to your domain, like `materia.mit.edu`
> * Display in Canvas Nav?: set true to have the Materia show up in the navigation menu in Canvas
> * LTI single sign on ONLY?: True if you're using Canvas and don't have a custom auth integration


If you need to change any settings not covered by the installer, be sure to edit the config files in `fuel/app/config/production/`.  If the config you are editing isn't already there, copy it there for editing.

The only exception is the LTI configuration, which is located in `fuel/app/modules/lti/config/production/lti.php`.


## SSL CERTIFICATE

If your network admin can provide production-ready SSL certificates, use those.

However, you may need to quickly create a temporary self-signed cert for the sake of testing.  If so, create one with this command.

```
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/materia-selfsigned.key -out /etc/ssl/certs/materia-selfsigned.crt
```

## Apache Configuration

Make sure the PHP and XSendfile modules are enabled.

```shell
$ sudo a2enmod rewrite php7.0 xsendfile ssl
$ sudo service apache2 restart
```

We'll set up Materia using virtual hosts in Apache.  This should allow Materia to share a server with other applications if needed.

Set up an Apache virtual host by copying the [**vhost configuration**](https://github.com/ucfopen/Materia/wiki/Apache-Virtual-Host-Configuration) from our wiki.

```shell
$ sudo nano /etc/apache2/sites-available/materia.conf
```

Enable the new virtual hosts.

```shell
$ sudo a2ensite materia
```

Depending on what else you have running on the server, you may want to disable the default Apache virtual host.

```shell
$ sudo a2dissite 000-default
```

Reload Apache to accept all of your changes.

```shell
$ sudo service apache2 reload
```

Test the homepage.

```shell
# If you have a domain set, use that instead of localhost
# should output the html title tag
$ curl -ks https://localhost | grep "<title>"
```

## File Permissions

The user that Apache runs as will need permissions to write to some of the directories.

That user is typically `www-data`. The directories outlined in our [install configuration](https://github.com/ucfopen/Materia/blob/issue/simplify-configs/fuel/packages/materia/config/install.php#L9) need to be writable by that user.

## Creating an Admin User

To use the admin interface, you'll need to create an admin user or add permissions to an existing user.

Quickly add a new user:

```shell
$ env FUEL_ENV=production php oil r admin:instant_user ~materia_admin
User Created
New password is ~materia_admin cy9mLFGK
~materia_admin now in role: basic_author
5
```

Add admin permissions to a user:

```shell
$ env FUEL_ENV=production php oil r admin:give_user_role ~materia_admin super_user
~materia_admin now in role: super_user
1
```

## Installing Widgets

You'll need some widgets!  Widget packages will be available for download or manually built from source if you have access to one.

You can install widgets from the command line or from the Admin interface. Learn more about [installing widgets](installing-widgets.html).
