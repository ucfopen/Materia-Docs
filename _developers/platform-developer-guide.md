---
title: Developing Materia Server
tagline: Customizing and Extending the Materia Platform
class: developers
category: platform
---
# Running and Developing a Materia Server

A local running copy of the server is useful to develop, test, or demo Materia.  This document outlines how to get a server running and will familiarize you with the various moving parts of Materia.

## Docker Development Environment

Our dev team uses Docker for all server development. See [Server Setup - Docker for Development](../admin/server-docker-development.html) to get started.

## Application Server Architecture

Materia has led a long life, changing iteratively as it ages. Although the FuelPHP framework is a Rails-like MVC, the entire code base is not yet taking full advantage of it. We do desire to head in that direction, but features and stability fixes have driven it's direction.  We welcome pull requests!

### Directory Structure

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



## Configuration

Materia completely relies on [FuelPHP's configuration mechanism](https://fuelphp.com/docs/classes/config.html), which can be a little complex.  The crux of it is, FuelPhp merges multiple config files together, starting with the base configs and over-writing variables from more specific configurations.

FuelPHP allows for configuration files for each "environment".  This means production, development, test, and staging all have their own settings that merge with the base configuration.

Materia attempts to consolidate the default configuration files are in `/fuel/app/config/`, however different modules and packages may contain their own configuration files.  It's suggested that you peruse that directory to familiarize yourself.


## User Authentication Modules

Materia uses FuelPhp's Auth package to handle basic authentication. This package allows for easy and modular integration with external user data.  View FuelPhp's documentation for details about [writing your own authentication drivers](http://fuelphp.com/docs/packages/auth/drivers.html)

### Object Permissions

Users have permissions to specific widget instances.  There are two permission levels defined in Materia:

* **Full**: the user has full control over the instance, options, and data.
* **View Scores**: the user only has permissions to collect score information.

### Roles

Materia has a very simple three-role system:

* **Admin** (role name - super_user): Can manage all instances, users, and install widget engines.
* **Instructor** (role name - basic_author): Can see and manage their own instances.
* **Student**: Can only create [guest widget instances](../create/getting-started.html#guest-widget-instances)

## Widgets

Materia hosts and manages various widget engines for use in course content. Below are a number of important details that will be useful when developing for the server.

> Keep in mind there's a whole seperate section for [Developing Widgets](widget-developer-guide.html).

### Widget Engines vs Widget Instances

Conceptually a **widget engine** is a specific kind of widget, like Hangman or Labeling.  Engines include the assets needed to display in the server's catalog and the code needed to customize and display content. Widget engines are installed by Materia admin users.

On the other hand, a **widget instance** is a single use of a widget engine, created and customized by an instructor for use in a course.

Instances tie a widget engine to specific useage data like:

* customized content and questions
* uploaded media (for engines that support images or audio content)
* instance settings like open and close dates
* player's scores

## API

The majority of the Materia API is defined in a single api class.  This API is used by the client's Angular.js code to communicate with the server.

> All new API development is expected to use FuelPHP's built in controlers more directly like the admin API has started to do.  It is expected to head in a more RESTFUL direction.

The older portions of the API were built with the following conventions:

* Input validation is executed **first**, followed by authorization, then method specific code.
* Method names **start** with nouns, the object or construct that is the subject of the api action.
* Method names **end** with a verb applied to the noun (widget_get, widget_instance_update, etc.).
* In case of an error or problem, return a `Msg` object with details about the error to the client.


## Unit Tests

Materia uses FuelPhp's built in unit tests that rely on PHPUnit.  We have built unit tests for the entire Materia API as well as for each score module. The tests are easily run using oil:

```shell
$ php oil test
```
