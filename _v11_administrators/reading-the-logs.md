---
title: Materia Log Files
tagline: Understanding the Materia Logs
class: admin
category: maintenance
highlighter: no
---
# Reading Materia's Log Files

## Application Logs

Materia uses the standard logging functionality built into Django. Logs are written to the top-level `logfile.log` in the application directory (`/var/www/html/` in the `python` container filesystem.) Logs are additionally emitted to `stdout` to be picked up and aggregated by docker.

## Gunicorn Logs

Materia uses Gunicorn as its WSGI server. Gunicorn logs are emitted to `stdout` alongside the application logs to ensure they can be seen by docker and handled by the host machine or passed off to a third-party logging service such as AWS CloudWatch.

## NGINX Logs

NGINX emits access and error logs to `/var/log/nginx/`, unless you override these paths in a custom NGINX configuration. The NGINX image used by Materia soft-links both logs to `stdout` and `stderr`, enabling docker log listeners to pick up on them in the same way as the application and gunicorn logs.

## Loggers in Docker Compose

You can add a `logging:` driver to individual services in your docker compose file. This allows your host machine to aggregate and manage logs coming from both docker containers. Review [docker's logging documentation](https://docs.docker.com/engine/logging/configure/) for more information about configuring a log driver in your compose file.