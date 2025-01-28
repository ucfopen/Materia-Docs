---
title: Getting Started
tagline: Learn what's required to setup Materia.
class: admin
category: first_time
---

# About Materia

Materia is an open-source platform and ecosystem for educational _widgets_ licensed under AGPL v3. "Widgets" are relatively self-contained web applications
that are installed to Materia. Instructors use a widget's **creator interface** to customize individual **instances** of a widget; these instances can then be
shared with students, who interact with the widget's **player interface**.

Materia is designed to operate standalone, or in conjunction with an LMS through LTI. Integration with an LMS offers several advantages, including user authentication
and gradebook passback when widgets are embedded as assignments.

> Casual references to "Materia" typically involve both the platform itself and its ecosystem of widgets. Note that widget code is completely independent of the platform, and widgets are developed, maintained, and installed separately.

Setup and administration of Materia involves several key steps:

1. Configuration of the application stack via docker compose.
2. Configuration of the application settings via environment variables.
3. Configuration of application networking.
4. Determining user authentication requirements.
5. Initialization and validation.
6. Installation and management of widgets.
7. LMS integration.

> Note that while you can clone the Materia repository locally, it is not required for a production instance of Materia.

## Architecture

Materia is a containerized web application designed to work with docker and docker compose. It consists of:

* An **application** container running php-fpm.
* A **webserver** container running NGINX, which reverse-proxies requests to the app container.

Additionally, a variety of ancillary containers may be included based on the current environment and your installation's needs:

* **Memcached**, for server-side caching.
* **MySQL**, as a local database solution. This is intended for development.
* **FakeS3**, to simulate AWS S3 as an asset storage solution. This is intended for development.

We use docker compose for container orchestration and networking. While it is possible to run Materia outside of a docker environment, **we do not recommend doing so.**

Materia's server application is built on top of [FuelPHP](https://fuelphp.com/), an open-source MVC framework. It requires a MySQL database (MariaDB will work too). The front-end code is written in React and makes use of the [React Query](https://tanstack.com/query/v4/docs/framework/react/overview) library to improve query performance.

## Requirements

### Host Machine Requirements

* Docker engine.
* Docker compose (provided through a separate plugin).
* Ports 80 and 443 (or configured equivalents).

## Configuration

Based on the steps highlighted above, let's take a brief walk through the configuration process.

### Configuring Your Installation with Docker

The key element to configure with docker is the `docker-compose.yml` file, or a composite of base yaml file and overrides. The compose file is responsible for:

* Determining the version of the application to be run.
* Loading of environment variables.
* Networking configuration.
* Mounting resources from the host machine.

The application is relatively self-contained within the `app` image, though certain resources specific to your instance of Materia should be mounted from the host.

For a deep dive into configuring your docker compose file(s), visit [Setting Up Docker](setup-configure-docker.html).

### Configuring Environment Variables

Materia has a number of critical settings that need to be configured before your instance is up and running. Because we want to avoid direct changes to FuelPHP configuration files within the application container, server settings are best written to **environment variables** that are then written to the container by docker compose.

Env variables can be referenced in your docker compose file in two ways:

```ini
app:
  env_file:
    - .env
	- .env.local # you can use multiple .env files if desired. Later entries take precedence.
  environment:
    - FUEL_ENV=production
	- BOOL_LTI_RESTRICT_LOGINS_TO_LAUNCHES=true 
```

We generally recommend storing your environment variables in a `.env` file that's then associated with the `env_file:` directive in the `app` service.

The [root .env file in the Materia repo](https://github.com/ucfopen/Materia/blob/master/.env) can serve as a template for your own `.env` file. We go into even greater detail on server variable configuration at the [Setting Up Server Variables page](setup-configure-env-vars.html).

### Network Configuration

We provide a baseline NGINX configuration that should be suitable for general needs, but specific changes may be required for your specific network requirements. If a custom NGINX configuration is not explicitly volume mounted, the [nginx-production](https://github.com/ucfopen/Materia/blob/master/docker/config/nginx/nginx-production.conf) configuration will be used.

Learn more about setting up your NGINX config at the [nginx configuration](nginx-configuration.html) page.

### User Authentication

Materia has two primary means of user authentication:

1. Direct authentication through an auth module. We do not provide an out-of-the-box solution for user management with this approach. In most circumstances, a custom auth module must be written to facilitate user authentication from an upstream login, such as SAML. While FuelPHP has command-line and programmatic tools to create new users, this is not sufficient for user management at scale.
2. Authentication from an LMS. This is the recommended approach. Materia uses LTI to facilitate user logins, and user records are prepared and updated based on the LTI authentication payload.

For the vast majority of institutional instances of Materia, we recommend deferring to the LMS for user authentication. We go into user account management in more detail on the [User Accounts](user-accounts.html) page.

### LMS Integration

Materia works with Instructure's Canvas out of the box. Read more at the [Canvas LTI Setup page](canvas-lti-setup.html). Note that the process of installing Materia in the LMS should occur once the application is running.

## Startup

With configuration covered, your remaining tasks should occur after the application is up and running.

```shell
docker compose up -d
```

> In production environments, we recommend the `-d` flag to allow the compose process to run in detached mode.

### Things To Do After Initialization

1. Configure service user accounts. We recommend creating a dedicated super user to facilitate widget administration. Visit the [User Accounts](user-accounts.html) page to learn more.
2. Install Materia in your LMS.
3. Install widgets.

### Widget Installation & Management

The Materia application image does not come with any widgets pre-installed; once your instance is running, you can install widgets through php cli commands or from the widget admin panel. The admin panel is accessible to users with the `super_user` role.

More information about widget installation is available on the [Installing Widgets](installing-widgets.html) page.

