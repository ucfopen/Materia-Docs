---
title: Server Requirements
tagline: What's needed to get Materia running
class: admin
category: [server, server_setup]
highlighter: no
---
# Production Server

The ideal server environment would look something like this:

* Linux (Tested with Alpine, Debian, Ubuntu, Solaris and Centos)
* Apache or nginx with host configuration access
* Two DNS addresses with SSL
  0. https://materia.example.com - Hosts the application
  0. https://static-materia.example.com - Hosts the js/css/html files and keeps the widgets sandboxed

## PHP Configuration
Default PHP.ini with exception of:

* `short_open_tag=On`

## PHP & Modules
* PHP 7.0
* bcmath
* gd
* mbstring
* mcrypt
* pdo_mysql
* xml
* zip

## Required Linux Packages for PHP modules
* libmcrypt-dev
* libmemcached-dev
* libpng-dev
* libxml2-dev
* zlib1g-dev

## Database
* Mysql 5.7 / MariaDB 10.2
* Two databases (on one Mysql server) - one for production and one for test

## Deploy and Build Requirements
Nodejs is needed to compile Sass & Coffeescript into CSS and javascript

* Nodejs
* PHP's Composer package manager
* PHP packages listed in composer.json

## Node Dependencies
* NPM
* Yarn
* NPM packages listed in package.json

## Optional & Recommended

We've seen reasonable performance without memcached, but recommend it.

* memcached server & memcached php module (pecl-memcached)

> Make sure to get the <code>memcache<b>d</b></code> module ending with a &quot;d&quot;, <b>NOT</b> the <code>memcache</code> module ending with a &quot;e&quot;!

