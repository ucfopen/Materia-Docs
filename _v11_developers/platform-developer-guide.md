---
title: Developing Materia Server
tagline: Customizing and Extending the Materia Platform
class: developers
category: platform
---
# Running and Developing a Materia Server

A local running copy of the server is useful to develop, test, or demo Materia.  This document outlines how to get a server running and will familiarize you with the various moving parts of Materia.

## Docker Development Environment

<<<<<<< Updated upstream
Our dev team uses Docker for all server development. See [Server Setup - Docker for Development](../admin/server-docker-development.html) to get started.
=======
Our dev team uses Docker for all server development. See [Server Setup - Docker for Development](../admin/setup-configure-docker.html) to get started.
>>>>>>> Stashed changes

## Application Server Architecture

Materia is witten in [Django](https://www.djangoproject.com/), a popular and well-established python framework. The server application contains a first-party API using [Django Rest Framework](https://www.django-rest-framework.org/). Within Materia, individual widgets can be written in any front-end language, provided they implement a number of required callbacks and possess a score module written in python.

### Directory Structure

Note: this is not a comprehensive breakdown of the entire Materia server file structure.

```ini
Materia
  app/
    api/                      # api app
      tests/                  # api test suites
      urls/                   # api url configuration
      views/                  # api views
      filters.py                # DRF filter backends
      paginators.py             # DRF pagination classes
      permissions.py            # DRF permissions classes
      serializers.py            # DRF serializers
    core/                     # core app
      management/               # django management commands
      migrations/               # database migrations
      services/                 # cross-app services
      templates/                # view rendering templates
      templatetags/             # tags utility for templates
      utils/                    # service utilities
      views/                    # view classes
      context_processors.py     # context processors for views
      mixins.py                 # view mixins for preprocessing
      models.py                 # model classes to facilitate the django ORM
    lti/                      # lti app
      ags/                      # classes to facilitate the LTI 1.3 Assignments & Grades Service
      services/                 # lti-specific services
      templates/                # lti view templates
      views/                    # lti-specific views
      mixins.py                 # lti-specific mixins for preprocessing
      urls.py                   # lti url configuration
    materia/                  # materia application configurations
    media/                    # media directory when the "file" asset driver is in use
    scoring/                  # scoring app
      module_factory.py         # factory class for widget score module instantiation
      module.py                 # base score module class that all widgets inherit
    storage/                  # storage driver configs
    util/                     # utility classes
  githooks                    # githooks for linting
  public/                     # publicly visible files and static assets
    css/
    img/
    js/
    dist/                     # static assets are emitted here in dev
```



## Configuration

Materia adheres to Django's settings format for server configurations. The base settings file is `app/materia/settings/base.py`, which imports all other settings files. In practice, Materia server configurations can be imported at any time via:

```python
from django.conf import settings
```

The majority of configurable options are sourced from environment variables, with default fallback values provided in most cases. Certain configurations may require custom overrides or settings unique to your institution: these can be placed in `app/materia/settings/extra.py`, which is best volume-mounted from the host machine. Refer to the Docker development page for more info.

### Object Permissions

Materia uses the `ObjectPermission` model to delegate user permissions, via generic relations, to various model instances across the system. Principally, this is used to manage user permission to individual widget instances. There are two permission levels defined in Materia, though the system allows for extensibility in the future:

* **Full**: the user has full control over the instance, options, and data.
* **View Scores**: the user only has permission to collect score information.

Additionally, `ObjectPermission` records can include a `context_id` value, which associates a user's access to an object to a specific course context. This is known as **Provisional Access** and allows delegation of access to resources associated with a course that a user may not otherwise have permissions to see.

### Roles

Materia has a very simple three-role system:

* **Admin** (django users with the `super_user` flag): Can manage all instances, users, and install widget engines.
* **Instructor** (django users in the `basic_author` group): Can see and manage their own instances.
* **Student**: Can only create [guest widget instances](../create/getting-started.html#guest-widget-instances).

## Widgets

Materia hosts and manages various widget engines for use in course content. Below are a number of important details that will be useful when developing for the server.

> Keep in mind there's a whole seperate section for [Developing Widgets](widget-developer-guide.html).

### Widget Engines vs Widget Instances

Conceptually a **widget engine** is a specific kind of widget, like Guess the Phrase or Labeling.  Engines include the assets needed to display in the server's catalog and the code needed to customize and display content. Widget engines are installed by Materia admin users.

On the other hand, a **widget instance** is a single use of a widget engine, created and customized by an instructor for use in a course.

Instances tie a widget engine to specific useage data like:

* customized content and questions
* uploaded media (for engines that support images or audio content)
* instance settings like open and close dates
* players' scores

## API

Materia uses a first-party API to facilitate communication of asynchronous resources between the front-end and server. The API is authored in Django Rest Framework, and split across a number of top-level endpoints:

```ini
/api/assets/               # user media asset management
/api/generation/           # AI-generated content
/api/notifications/        # user notifications
/api/play-sessions/        # play session management
/api/storage/              # play-related data storage management
/api/scores/               # instance score data
/api/sessions/             # user sessions
/api/users/                # user data (NOT management)
/api/instances/            # widget instance management
/api/widgets/              # widget engine management
```

When API endpoints map to a model, API classes inherit DRF's `ModelViewSet` class. We use DRF serializers and permissions classes to manage data transformation and check access permissions on a model and model instance level.

## Tests

Materia uses Django's built-in testing framework and are mapped to the API. GitHub workflows automatically run the test suite on push and pull request.

<<<<<<< Updated upstream
```
=======
```shell
>>>>>>> Stashed changes
$ python manage.py test
```
