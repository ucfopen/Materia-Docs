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
  webserver:
  image: ghcr.io/ucfopen/materia:webserver-stable # webserver image with webserver-stable tag
    ports:
      - "80:80" # main materia
      - "443:443" # main materia
    networks:
      - frontend
    depends_on:
      - app

  app:
    image: ghcr.io/ucfopen/materia:app-stable    # app image with app-stable stag
  networks:
    - frontend
    - backend

  networks:
    frontend:
      name: materia_frontend
    backend:
      name: materia_backend

  volumes:
    static_assets: {}
```

In this sample compose file, you'll notice `image:` definitions for the `app` and `webserver` services. These specify the images associated with these services, including a version tag, published to the [GitHub package registry](https://github.com/ucfopen/Materia/pkgs/container/materia). We publish a number of different tags you can use to target specific image versions:

* Version tags for `app-` and `webserver-` and generated with every version release, including `-alpha` and `-rc`, based on [semantic versioning](https://semver.org/), for example `app-v10.3.0` or `webserver-v10.3.1-alpha.2`. We **generally recommend using versioned images for your production compose files** associated with stable releases.
* `app-dev` and `webserver-dev` are generated with every version tag, including `-alpha` and `-rc` releases. These often represent in-development versions of Materia and are not generally desireable for production use.
* `app-stable` and `webserver-stable` are only generated with final releases, either `major`, `minor`, or `patch` as dictated by semantic versioning. While safe for production, the `-stable` tags are applied to the most recent final release, and using them in production may result in unintended behavior.

If you're not sure what application version to target, peruse the [releases page on GitHub](https://github.com/ucfopen/Materia/releases) to select the semver value for the most recent release.

> Note that if you are using custom built images of the `app` service, your custom image should supercede the image url and tag defined here. We recommend tagging your images with semver to ensure parity.

## Updating Materia

Because the Materia application is largely self-contained in docker containers, updates to the application are reasonably straightforward. This upgrade process makes several assumptions about your production Materia instance:

1. No direct changes have been made to files on the container fileystems (`app` and `webserver`), such as modifications to server-side files. These changes are inherently ephemeral and will be lost as soon as the container is destroyed and rebuilt.
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

Next, update your compose file with the tags associated with the new release. At minimum, `app` and `webserver` should be updated:

```ini
services:
  app:
	image: ghcr.io/ucfopen/materia:app-v10.3.0 # use the semver value for the new version if using version tags

	...additional app definitions...

  webserver:
	image: ghcr.io/ucfopen/materia:webserver-v10.3.0 # use the semver value for the new version if using version tags

	...additional webserver definitions...
```

Note that if `-stable` tags are in use, this step is not necessary, as the newest stable releases are always tagged with `-stable`.

### Pull Images and Restart Containers

Next, pull the new versions of the images and restart the compose process:

```shell
docker compose pull
docker compose up -d
```

### Perform Post-Update Tasks

If required, post-update commands (such as database migrations) are typically run in the context of the `app` container. Review the container shell access commands on our [docker setup](setup-configure-docker.html) page and run any needed tasks within the `app` container. For example, to perform a database migration:

```shell
<host machine shell>  $ docker exec -it <app container id> sh
<app container shell> $ php oil r migrate
```
