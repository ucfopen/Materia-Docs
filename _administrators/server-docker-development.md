---
title: Server Setup - Docker for Development
tagline: How to use Materia-Docker to run a development server
class: admin
category: [server, server_setup]
highlighter: no
---
# Running a Development Docker Server

Docker is the fastest way to get a local copy of Materia Server running. Materia has a built in [collection of scripts and configuration files](https://github.com/ucfopen/Materia/tree/master/docker) needed to orchestrate a local development Docker environment.

Our developers use this setup to run tests, develop new features, and to qa widgets.  It has containers available for all optional hosting features in Materia.

To get started, clone Materia, cd into the `docker` directory, and execute `./run_first.sh`.

The [Materia Docker README](https://github.com/ucfopen/Materia/blob/master/docker/README.md) will get you up and running in less than 10 minutes.

