---
title: Materia Development Server
tagline: Customizing and Extending the Materia Platform
class: developers
category: platform
---
# Overview

[ucfopen/Materia-Docker](https://github.com/ucfopen/Materia-Docker) is the fastest way to get started *developing* and *testing* Materia Server. It's a collection of scripts and configuration files needed to orchestrate a local development Docker environment.

Our developers use this setup to run tests, develop new features, and to qa widgets.  It has containers available for all optional hosting features in Materia.

To get started, clone Materia-Docker into a directory and execute `./run_first.sh`.  The README from Materia-Docker will get you up and running in less then 10 minutes.

# Application Architecture

Materia has led a long life, changing iteratively as it ages. Although the FuelPHP framework is a Rails-like MVC, the entire code base is not yet taking full advantage of it. We do desire to head in that direction, but features and stability fixes have driven it's direction.  We welcome pull requests!

## Directory Structure

```shell
Materia
├── fuel
│   ├── app
│   │   ├── classes
│   │   │   ├── controller      # all routes call a controller method
│   │   │   ├── materia         # much of materia's non-fuelphp logic
│   │   │   ├── model
│   │   │   ├── service         # services to create skinnier models
│   │   │   ├── trait           # make use of Traits as much as possible: DRY
│   │   │   └── view
│   │   ├── config              # majority of configuration files here
│   │   ├── logs                # application log files
│   │   ├── media               # uploaded media content from widgets
│   │   ├── migrations
│   │   ├── modules
│   │   │   └── lti
│   │   │       ├── classes
│   │   │       ├── config      # don't forget, lti configuration files are in here
│   │   │       ├── migrations  # the lti module has it's own migrations
│   │   │       └── tests
│   │   ├── tasks               # this is where all the cli task code is
│   │   ├── themes              # Most of our views (templates) are here
│   │   └── tmp
│   ├── core                    # fuelphp core package
│   └── packages
│       ├── ltiauth             # auth module for lti use
│       └── materiaauth         # base materia auth module
├── githooks                    # githooks for testing and linting
└── public                      # the only publicly hosted files
    ├── css
    ├── img
    ├── js
    └── widget                  # all widget engines get installed here
```


# Configuration

Materia completely relies on [FuelPHP's configuration mechanism](https://fuelphp.com/docs/classes/config.html), which can be a little complex.  The crux of it is, FuelPhp merges multiple config files together, starting with the base configs and over-writing variables from more specific configurations.

FuelPHP allows for configuration files for each "environment".  This means production, development, test, and staging all have their own settings that merge with the base configuration.

Materia attempts to consolidate the default configuration files are in `/fuel/app/config/`, however different modules and packages may contain their own configuration files.  It's suggested that you peruse that directory to familiarize yourself.

# Permissions system

## Object Permissions

Users have permissions to specific widget instances.  There are two permission levels defined in Materia:

* **Full**: the user has full control over the instance, options, and data.
* **View Scores**: the user only has permissions to collect score information.

## Roles

Materia has a very simple three-role system:

* **Admin** (role name - super_user): Can manage all instances, users, and install widget engines.
* **Instructor** (role name - basic_author): Can see and manage their own instances.
* **Student**: Can only create [guest widget instances](../create/getting-started.html#guest-widget-instances)

# User Authentication Modules

Materia uses FuelPhp's Auth package to handle basic authentication. This package allows for easy and modular integration with external user data.  View FuelPhp's documentation for details about [writing your own authentication drivers](http://fuelphp.com/docs/packages/auth/drivers.html)

# Unit Tests

Materia uses FuelPhp's built in unit tests that rely on PHPUnit.  We have built unit tests for the entire Materia API as well as for each score module. The tests are easily run using oil:

```shell
$ php oil test
```
