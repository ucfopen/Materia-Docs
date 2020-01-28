---
title: Server Setup - Windows and Apache
tagline: How to set up a Windows LAMP server for hosting Materia in Production
class: admin
category: [server, server_setup]
---

# Installing Materia on Windows

This install guide runs you through installing Materia and all the required software on a brand new Windows Server 2012 machine.  You may have some pieces of this already installed if you're already running a web server.

This guide was created for an older version of Materia. It's here as an example, but we suggest using the current release version, which is likely to have updated requirements.

The dependencies to run Materia are included in the Dockerfiles we use for local development. Those can be found in the Materia repository here: [PHP webserver dockerfile](https://github.com/ucfopen/Materia/blob/master/docker/dockerfiles/materia-web) and [node dockerfile](https://github.com/ucfopen/Materia/blob/master/docker/dockerfiles/materia-node). Note that you don't need 2 servers, we just use 2 containers to isolate responsibilities.

## Versions used in this Guide

* Last Updated **May 2018**
* Materia version: **v3.6.2**
* Windows version: **Windows Server 2012**
* Apache version: **2.4.28**
* MySQL version: **5.7.20**
* PHP version: **7.0.25**

## Relax Windows Security (optional?)

Some of the default security settings on a new 2012 server make getting the software and setting up the server a chore.  Here's our temporary suggestions to make the install easier.

In the **Server Manager**, go to the **Dashboard tab**. It should be the default tab.

Click **Configure this local server**.

Click 'On' next to 'IE Enhanced Security Configuration' and turn it **off** - at least for administrators.

Click 'Private: On' next to 'Windows Firewall'. Click 'Turn Windows Firewall on or off'. **Disable the private firewall**.

Open Internet Explorer. **Don't** use recommended security settings.


## A Unix Compatable Text Editor

Materia files will appear to be a single line on Windows (it's written with unix line endings), so you'll want a text editor that can display them properly.

Download [Notepad++](https://notepad-plus-plus.org/), [SublimeText](https://www.sublimetext.com/), [Atom](https://atom.io/) or other text editor you prefer to make editing the config files easier.


## A .tgz Extraction Utility

Materia releases will be compressed and packaged in `.tgz` files, for which you will need an extraction utility. [7-Zip](https://www.7-zip.org/) is recommended, as it is both free and very easy to use.


## Git for Windows

Git is no longer required to download and install Materia, but the tools available in [Git for Windows](https://gitforwindows.org/) are still helpful for some of the other steps in the process - specifically, Git Bash is a more readily usable terminal application over Command Prompt, and also enables the usage of an OpenSSL plugin for generating our own SSL certificates (more on that later).

All of the default selections in the Git for Windows installer will suffice.


## Install MySQL Server

Download the [MySQL Installer](http://dev.mysql.com/downloads/windows/installer). Make sure to get the correct version of MySQL - Materia uses MySQL 5.7.

Click **Looking for previous GA versions?** and select the most recent version of MySQL 5.7. You can also go [straight to the 5.7 installer selection page](https://dev.mysql.com/downloads/windows/installer/5.7.html).

Choose the **larger** of the two (currently `mysql-installer-community-5.7.22.1.msi`).

> Select the **No thanks** option to download without signing up.

Run the installer.

Choose **Custom** as setup type.

In the list, choose the following items from the list on the left to install by clicking the green arrow pointing right:

**MySQL Servers** -> **MySQL Server** -> **MySQL Server** (currently 5.7) -> **MySQL Server** - X64

**Applications** -> **MySQL Workbench** -> **MySQL Workbench** (currently 6.3) -> **MySQL Workbench** - X64

**Applications** -> **MySQL Utilities** -> **MySQL Utilities** (currently 1.6) -> **MySQL Utilities** - X64

Click `Next`.

The MySQL Installer will indicate that there are uninstalled requirements, but it will take care of these for you once you click `Execute`.

Once the components are installed, clicking `Next` should begin configuration of the MySQL server.

Select **Standalone MySQL Server / Classic MySQL Replication** and click `Next`.

Leave everything alone on the first page, and click `Next`.

In the Accounts and Roles page, set the root user password as desired. **Remember this password.**

Click `Next`.

Click `Next` again.

Click `Next` again.

Click `Execute`.

Once everything is done running, click `Finish`.

Click `Next`.

Click `Finish`.

## Create Materia Database and User

Start the MySQL Workbench application.

Ignore the warning from MySQL Workbench regarding the operating system, it will work just fine.

Click the only MySQL instance available and use the **root** user password set earlier.

If a 'Query 1' tab is not already available, click the 'Create a new SQL tab for executing queries' (leftmost) button to create a new tab to run queries in.

Insert the following text:

```sql
CREATE DATABASE IF NOT EXISTS `materia`;
CREATE USER 'materia'
IDENTIFIED BY 'YOUR_PASSWORD_HERE';
GRANT ALL ON `materia`.* TO 'materia'@'%';
```

Be sure to replace `'YOUR_PASSWORD_HERE'` with a password of your choosing. **Remember this password!** You'll need it later!

Click the leftmost 'Execute' (lightning bolt) button to run the above queries.

After the queries run, close the workbench.


## Install Apache Web Server

Go to [ApacheHaus](http://www.apachehaus.com/cgi-bin/download.plx) and download the latest package including OpenSSL

Extract the `Apache24` folder to the root of the `C:/` drive (Result: **C:/Apache24**).

In the command prompt (not PowerShell):

```shell
cd C:/Apache24/bin
httpd -k install
httpd -k start
```

Verify that Apache is working by visiting [localhost](http://localhost) in your browser.

## Add XSendFile to Apache

Download [mod_xsendfile](https://github.com/nmaier/mod_xsendfile), click `Clone or Download` then `Download ZIP`.

Extract the contents of the file.

Copy **mod_xsendfile-master/bin/Apache24/Win64/mod_xsendfile.so** to **C:/Apache24/modules/mod_xsendfile.so**.

Edit **C:/Apache24/conf/httpd.conf** and add the following line under the last instance of **LoadModule**:

```apache
LoadModule xsendfile_module modules/mod_xsendfile.so
Define APACHE_LOG_DIR "/Apache24/logs"
```

Locate the line reading `#LoadModule rewrite_module modules/mod_rewrite.so` and remove the '#' at the beginning of the line.

Save those changes.

In Command Prompt:

```shell
cd C:/Apache24/bin
httpd -k restart
```

## Add Apache to Path


Go to Windows **Control Panel** -> **System and Security** -> **System**.

Click **Advanced System Settings**.

Click **Environment Variables**.

Under 'System variables' find **Path**, then click `Edit...`.

Append `;C:\Apache24\bin` to the end of the 'Variable value' field and click `OK`.


## Install PHP and Extensions

Download the appropriate [Thread Safe PHP 7.0 zip file](http://windows.php.net/download/).

Extract the .zip file's contents to `C:/php`.

Rename to `C:/php/php.ini-production` to `C:/php/php.ini`, then open it in your editor.

Change the line containing **extension_dir** and to `extension_dir = "C:\php\ext"`, note the lack of a semicolon at the front.

Change the value of **session.save_path** to `C:\Temp`.

Locate and **remove** the semicolon at the beginning of each of the following lines:

1. `;extension=php_fileinfo.dll`
2. `;extension=php_gd2.dll`
3. `;extension=php_mbstring.dll`
4. `;extension=php_openssl.dll`
5. `;extension=php_pdo_mysql.dll`


## Add PHP to Path

Return to Windows **Control Panel** -> **System and Security** -> **System**

Click **Advanced System Settings**.

Click **Environment Variables**.

Under 'System variables' find **Path**, then click `Edit...`.

Append `;C:\php` to the end of the 'Variable value' field and click `OK`.

## Add PHP module to Apache

Edit **C:/Apache24/conf/httpd.conf** again.

Find the last `LoadModule` and add the following directly under it:

```apache
LoadModule php7_module "c:/php/php7apache2_4.dll"
AddType application/x-httpd-php .php
PHPIniDir "C:/php"
```

Find the area containing `<IfModule dir_module>`.

Change `DirectoryIndex index.html` to `DirectoryIndex index.html index.php`.

Save the file.


In Command Prompt:

```shell
cd C:/Apache24/bin
httpd -k restart
```

## Test PHP

Create the following `index.php` file in `C:/Apache24/htdocs`.

```php
<?php phpinfo(); ?>
```

Now visit [http://localhost/index.php](http://localhost/index.php) to verify the php info page is functioning.


## Create SSL Certificate for HTTPS

In Git Bash:

```shell
mkdir C:\ssl\private
mkdir C:\ssl\certs
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout C:\ssl\private\materia-selfsigned.key -out C:\ssl\certs\materia-selfsigned.crt
```

Provided the requested information at each prompt.


## Add Materia Virtual Host to Apache

Edit **C:/Apache24/conf/httpd.conf** again.

Find a line reading '# Virtual Hosts'. Under this line, add:

```apache
Include conf/extra/materia-vhosts.conf
```

Create and edit the file 'C:/Apache24/conf/extra/materia-vhosts.conf'.

Follow [Materia Apache Virtual Host Configuration](https://github.com/ucfopen/Materia/wiki/Apache-Virtual-Host-Configuration).

Substitue the following values:

* replace all `materia.YOUR_DOMAIN.edu` with your domain (`localhost` for now)
* replace all `/var/www/materia` with `C:/materia`
* replace all `/etc/ssl/` with `C:/ssl`


## Install Materia

Look up the list of [Materia releases] (https://github.com/ucfopen/Materia/releases) and download the .tgz package containing the latest Materia release.

Once downloaded, use 7-Zip to extract this package to `C:/materia`.

You will also need to create a couple of directories before running the installer, as it will expect them to exist:

```shell
cd C:/materia
mkdir fuel/app/logs
mkdir public/widget
mkdir public/widget/test
```


## Configure Materia

Run the Materia Installer.

```shell
php oil refine install
```

When prompted to choose an environment, type `production`.

When prompted to run the configuration wizard, type `y`.

We're using xsendfile, so type `true` at the next prompt.

If you'd like to be able to install widgets through the web administration panel, type `true` at the next prompt.

Type `file` at the next prompt to choose a cache driver, then press Enter to accept the default file location (or specify one if you want).

Type `file` at the next prompt to choose a session driver, then press Enter to accept the default file location (or specify one if you want).

When prompted to change the PDO connection string, type the following: `'mysql:host=localhost;dbname=materia'`

For the next two options, make sure your Database user name and password match what you defined earlier in the MySQL step. If you'd like to use the generated password, make sure to make the appropriate changes through the MySQL Workbench.

Choose an LTI identifier in the next option. This should probably match the basic URL for your instance of Materia.

Unless you know better, use the default values for the LTI username variable.

If you want Materia to appear as a navigation option in the Canvas side bar, type 'true' at the next prompt. If not, 'false'.

Set shared secret and consumer keys in the next two steps - make sure your LTI installations match these later.

If you'd like to restrict logins for your instance of Materia to the LTI only (no users can log in through SAML or the normal Materia login interface), type 'true' at the next prompt. If you'd like to allow normal logins, type 'false'.

The next five options offer default values for various salts and encryption keys - you can use specific values if you desire, otherwise press 'Enter' to accept the defaults.

When prompted to make paths writable, type `y`.

When prompted to clear server cache, type `y`.

When prompted to run migrations, type `y`.

When prompted to populate user roles, type `y`.

When prompted to populate default semesters, type `y`.

> NOTE: the semester generator is written for UCF, in which case you'll have to handle generating those yourself in the [Semester script](https://github.com/ucfopen/Materia/blob/master/fuel/packages/materia/tasks/semester.php).

When prompted to create default users, type `y`. This will print out a set of default users and the generated passwords for those users - keep track of these.

Restart Apache:

```shell
cd C:/Apache24/bin
httpd -k restart
```


## Updating Materia

Updating the version of Materia running on your server is as simple as downloading the latest release package and replacing the Materia directory with the new package, with one important exception: the production configuration.

To prepare, in Git Bash, move into the C directory with `cd C:/`.

Back up the current version of Materia by moving it to a different location with `mv materia materia-backup` or any other desired location - again, this is just a backup.

Follow the [instructions listed above](#install-materia) to install Materia - again, get the latest .tgz package from [Materia releases](https://github.com/ucfopen/Materia/releases) and extract it to `C:/materia`, overwriting the old files in the process. You won't need to run the install wizard again.

There are a few quick commands you'll need to run in order to copy some of the backed up material into the new files and prepare them for use:

```shell
cp -r materia-backup/fuel/app/config/production materia/fuel/app/config/
cp -r materia-backup/fuel/app/modules/lti/config/production materia/fuel/app/modules/lti/config/
cp -r materia-backup/fuel/app/logs materia/fuel/app/
cp -r materia-backup/public/widget materia/public/
cp -r materia-backup/fuel/packages/materia/media materia/fuel/packages/materia/
cd materia
php oil refine admin:make_paths_writable
```

This will bring everything back up to working order.

Be sure to read release notes carefully to stay aware of any updates to Materia, and to see if any migrations are necessary.

If an update to Materia requires migrations, you can run those migrations easily:

```shell
php oil refine migrate
```
