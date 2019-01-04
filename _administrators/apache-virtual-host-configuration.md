---
title: Server Setup - Apache Virtual Host
tagline: A guide to configuring an Apache Virtual Host to work with Materia
class: admin
category: [server, server_setup]
---

# Production ready Apache configuration.

## Features
* http redirects to https
* FuelPhp Environment set to `production`
* XSendFile enabled for speedy media delivery
* Max Upload set to 50MB for widget package uploading
* PHP execution restricted to FuelPHP's entry script
* All other PHP scripts do not execute, and result in a 404
* All "dot" files and directories result in a 404

## Find and Replace these strings as needed

* `materia.YOUR_DOMAIN.edu`
* `/var/www/materia`
* `/etc/ssl/certs/materia-selfsigned.crt`
* `/etc/ssl/private/materia-selfsigned.key`

``` apache
<VirtualHost *:80>
    # Redirect all traffic to HTTPS
    ServerName materia.YOUR_DOMAIN.edu
    Redirect "/" "https://materia.YOUR_DOMAIN.edu/"
</VirtualHost>

<VirtualHost *:443>
    ServerName materia.YOUR_DOMAIN.edu
    DocumentRoot /var/www/materia/public
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    # Setup SSL
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/materia-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/materia-selfsigned.key

    # SET the FUELPHP environment
    SetEnv FUEL_ENV production

    # Block php execution by default
    php_value engine off

    # route 404 and 403 errors to FuelPHP
    ErrorDocument 404 /index.php?/404
    ErrorDocument 403 /index.php?/403

    # Main application directory
    <Directory /var/www/materia/public>
        # Add xsendfile for faster/efficient media downloads
        XSendFile On
        XSendFilePath /var/www/materia/fuel/packages/materia/media

        # Allow the fuelphp .htaccess file to do it's thing
        AllowOverride All

        # Allow for sizable widget uploads to the admin panel
        php_value upload_max_filesize "50M"
        Require all granted
    </Directory>

    # turn on php for index.php
    <FilesMatch "^index.php">
            php_value engine on
    </FilesMatch>

    # hide/disable php for every script thats not index.php
    <FilesMatch "^(?!index).*(\.php)">
        # this isn't needed, but just to make it obvious...
        php_value engine off

        Require all denied
    </FilesMatch>

    # Static files
    <DirectoryMatch "^/var/www/materia/public/(widget|css|js|img)/">
        # allows symlinks to support zero downtime deploy mechanisms
        Options FollowSymLinks

        # block .htaccess files
        AllowOverride None
    </DirectoryMatch>

    # Block access to all dot files and directories
    <DirectoryMatch "^\.|\/\.">
        Require all denied
    </DirectoryMatch>

</VirtualHost>
```
