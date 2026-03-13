---
title: Versioning and Updating
tagline: Learn how to version and update Materia.
class: admin
category: first_time
---

# Managing Materia Versions

As discussed in our [getting started](getting-started.html) and [docker setup](setup-configure-docker.html) guides, your docker compose file is the primary means by which you'll manage and configure the version of Materia to be run:

```ini
services:
  nginx:
    image: ghcr.io/ucfopen/materia-django/nginx:11.0.0 # nginx image with 11.0.0 version tag
    restart: unless-stopped
    ports:
      - "80:80"
    networks:
      - frontend
      - backend
    depends_on:
      - python

  python:
    image: ghcr.io/ucfopen/materia-django/python:11.0.0 # python image with 11.0.0 version tag
    restart: unless-stopped
    networks:
      - backend
    ports:
      - "8001:8001"
      - "25:25"
    command: gunicorn materia.wsgi:application --bind 0.0.0.0:8001 --workers 4 --threads 2 --timeout 150 --log-level warning --access-logfile - --error-logfile -

  networks:
    backend:
    frontend:

  volumes:
    static_files:
```

In this sample compose file, you'll notice `image:` definitions for the `python` and `nginx` services. These specify the images associated with these services, including a version tag, published to the [GitHub package registry](https://github.com/ucfopen/Materia/pkgs/container/materia).

Packages are automatically published whenever a new version tag is applied to the Materia repository, including for pre-release builds such as `-alpha`, `-beta`, and `-rc`. These prerelease packages are generally intended for development and not suitable for production use. Stable versions (without an affix such as `-alpha`: `11.0.0`, `10.3.3`, and so on) are considered production-ready.

If you're not sure what application version to target, peruse the [releases page on GitHub](https://github.com/ucfopen/Materia/releases) to select the semver value for the most recent release.

> Note that if you are using custom built images of the `app` service, your custom image should supercede the image url and tag defined here. We recommend tagging your images with semver to ensure parity.

## Updating Materia

Because the Materia application is largely self-contained in docker containers, updates to the application are reasonably straightforward. This upgrade process makes several assumptions about your production Materia instance:

1. No direct changes have been made to files on the container fileystems (`python` and `nginx`), such as modifications to server-side files. These changes are inherently ephemeral and will be lost as soon as the container is destroyed and rebuilt.
2. The database is not hosted directly within the mysql container on a virtual volume. One exception is if the database itself (not the mysql process) is located on the host machine and volume mounted into the mysql container, in which case the database will persist across containers.
3. Environment variables, configs and credentials (such as NGINX), widget engine files, and user media files (if applicable) are volume mounted from the host machine.

### Back Up Critical Data

Ensure all critical persistent data is properly backed up before performing any kind of update, including:

1. User uploaded asset data (likely volume-mounted from the host machine),
2. Widget engine files (volume-mounted from the host machine),
3. The database,
4. Any other volume-mounted configuration files (NGINX, etc) or customizations.

Consult the release notes on GitHub associated with the version you intend to update to for specific considerations associated with the release. These may include things like database migrations or server configuration changes.

### Stop and Remove Running Containers

The currently running containers must be stopped and removed. If your compose process is running in a detached state, use `docker compose stop <container name or id>` to halt each container associated with Materia, followed by `docker compose rm <container name or id>` to destroy them.

### Update Your Compose File

Next, update your compose file with the tags associated with the new release. At minimum, `python` and `nginx` should be updated:

```ini
services:
  nginx:
    image: ghcr.io/ucfopen/materia-django/nginx:11.0.0 # use the semver value for the new version

    ...additional nginx service definitions...

  python:
    image: ghcr.io/ucfopen/materia-django/python:11.0.0 # use the semver value for the new version

    ...additional python service definitions...
```

### Pull Images and Restart Containers

Next, pull the new versions of the images and restart the compose process:

```shell
docker compose pull
docker compose up -d
```

### Perform Post-Update Tasks

If required, post-update commands (such as database migrations) are typically run in the context of the `app` container. Review the container shell access commands on our [docker setup](setup-configure-docker.html) page and run any needed tasks within the `python` container. For example, to perform a database migration:

```shell
<host machine shell>  $ docker exec -it <python container id> sh
<app container shell> $ python manage.py migrate
```
