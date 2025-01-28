---
title: Setting up Docker
tagline: Manage the docker compose configuration for Materia.
class: admin
category: first_time
---

# Docker in Depth

Materia uses Docker to simplify application installation, configuration, and management. The application consists of two primary images:

1. The `app` image contains the server-side application and precompiled static assets (javascript and CSS). While we use composer to manage and install server-side packages and webpack to compile static assets, the `app` image contains these files already.
2. The `webserver` image comes with a preconfigured NGINX config, though your installation's requirements may require adjustments. It serves static files directly from the app image through a virtual volume, and reverse-proxies PHP requests to the `app` container.

We use docker compose to orchestrate these containers, which allows us to take advantage of networking between containers. If you're not familiar with docker, we recommend taking a moment to become familiar with the concept of `containers`, `images`, `volumes`, and `docker compose` before continuing.

## Why You Don't Need the Materia Repository

Perhaps unintuitively, you don't actually need to clone the Materia repo for production instances of Materia. Because the `app` and `webserver` containers are production-ready, they contain all the code you need to run Materia, configuration aside.

You'll need to clone Materia if you intend to make code contributions or changes (for example, your institution desires modified versions of certain pages). Don't forget that Materia is licensed under AGPL v3, which requires modifications to be publicly available.

Additionally, the Materia repo comes with a non-development setup script (`docker/run_first_for_nondev.sh`), which can be used to help configure your docker compose files and environment variables. If you go that route, we recommend using the generated compose files and `env` files to inform a final set of compose and environment variable configurations that you'll use for a production setup. After that, you're free to ditch the repo.

## Volumes and Data Persistence

Because docker containers are ephemeral, you should **never make important changes to files directly in the filesystem of containers**. These changes will only persist for the lifetime of that specific container!

Instead, customization and changes to the filesystem of the containers - such as configuration files, user-created media, and installed widget files - should be volume-mounted from the host machine, to ensure these changes persist and to better facilitate backups and management. We'll go over what needs to be volume mounted in more detail in a moment.

## What About a Database Container?

We _strongly_ recommend you use an external database solution with Materia, such as a AWS RDS, Azure database for MySQL, or GCP's Cloud SQL for MySQL. While we ship a mysql container in the development docker compose configuration, you should **not use a container-mounted database in production**, due to data persistence considerations.

One potential exception is if you choose to volume mount the database file from the host machine. We don't recommend this approach, but it's safer than hosting the database directly in the container.

## Creating Your Compose File

_At minimum_, your docker compose file will look like the following:

```ini
services:
  webserver:
  image: ghcr.io/ucfopen/materia:webserver-stable
    ports:
      - "80:80" # main materia
      - "443:443" # main materia
    networks:
      - frontend
    depends_on:
      - app

  app:
    image: ghcr.io/ucfopen/materia:app-stable
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

One significant element to this base compose config is the version of the application to be run: this is defined by the tags associated with the `webserver` and `app` `image:` definitions. For a closer look at versioning, consult the [versioning and updating guide](versioning-and-updating.html).

The `app` and `webserver` service definitions will need to be expanded to include **volume mounts** and **environment variables**. These may vary depending on your local configuration needs:

```ini
services:
  webserver:
    volumes:
      - static_assets:/var/www/html/public:ro                       # mounts static assets shared volume
      - ./widget/:/var/www/html/public/widget/:ro                   # mounts local widget engine files
      - ./config/nginx/key.pem:/etc/nginx/conf.d/key.pem:ro         # mounts local nginx key
      - ./config/nginx/cert.pem:/etc/nginx/conf.d/cert.pem:ro       # mounts local nginx cert
      - ./config/nginx/nginx-dev.conf:/etc/nginx/nginx.conf:ro      # mounts nginx config

  app:
    env_file:
      - .env                                                        # env variables file and relative path
    volumes:
      - static_assets:/var/www/html/public                          # static assets shared volume
      - ./widget/:/var/www/html/public/widget/:rw                   # mounts local widget engine files
      - ./media/:/var/www/html/fuel/app/media/:rw                   # mounts local media assets
```

### Configuring Volume Mounts

Volume mounts enable data persistence across containers. Remember that docker containers are ephemeral! Any changes to a container's filesystem outside of volume mounts will be lost
should the container be killed and replaced. Volume mounts should include:

_For the webserver service:_

* The **NGINX config**: we recommend your NGINX configuration is maintained on the host machine and volume-mounted to `/etc/nginx/nginx.conf` on the webserver container.
* **Certificate and key pair**: Make sure your NGINX configuration properly references the key and cert. Note that if SSL is terminated ahead of the host machine (for example, with a load balancer), you may not need a key and cert.

_For the app service:_

* **User media**: when using the `file` asset storage driver, user-uploaded media will be stored (and served) from the filesystem. The media directory should be volume-mounted to ensure these assets persist.

_For both services:_

* **Static assets** The `static_assets` virtual volume definition means these files are not present on the host machine; they are sourced from the `app` service and cross-mounted to the `webserver` so NGINX can serve them.
* **Widget engine files**: These must be volume mounted to both services to ensure the files can be served by NGINX and referenced by the application when needed.

> Note the `:ro` and `:rw` flags for certain volume mounts. `:ro` (read-only) is recommended for webserver mounts since writes are not required on this container. `:rw` is recommended for the `widget/` and `media/` directories to ensure FuelPHP can write to them. You may need to adjust the ownership of these directories on the host machine if FuelPHP emits errors about write permissions.

## Host Machine Files & Permissions

The directory where Materia is located on your host machine ( we generally recommend `/var/www/html/`) should look something like this:

```ini
├── docker-compose.yml
├── nginx.conf
├── nginx/
│   ├── key.pem
│   └── cert.pem
├── media/
└── widget/
```

As mentioned above, double-check ownership of the `media/` and `widget/` directories to ensure FuelPHP can write to the volume-mounted locations on the `app` container's filesystem.

> While it's generally advised to limit permissions of your `nginx.conf` file, we have seen issues associated with the file not being permissive enough to be accessed in the webserver filesystem in certain versions of Debian. You may need to make this file more permissive to fix this issue.

## Running the Application

Use the `docker compose` command to run Materia:

```shell
docker compose up -d
```

As mentioned in Getting Started, we generally advise using the `-d` flag to run compose detached; otherwise, the terminal window running the process must remain open. This is acceptable for local development, but not ideal for production.

> Note that compose v1 used `docker-compose` for invocation. If you see errors associated with `docker compose` not being a valid command, you may be using a very outdated version of docker engine and compose.

The default `docker compose up` command assumes you are using a single `docker-compose.yml` and potentially `docker-compose.override.yml`. These two files are implicitly referenced by compose (with the override taking precedence). If you named your compose file something different, you should invoke the command with a reference to your compose filename:

```shell
docker compose up -d docker-compose.custom.yml
```

## Container Monitoring & Shell Access

Use `docker ps` to inspect your currently running containers. `docker ps` will provide the container ID hash (we'll need that for shell access), and provides an easy means of verifying the version of the image in use. For example,

```shell
ghcr.io/ucfopen/materia:app-v10.3.0
```

Lets us know that the `app` container in service is on `v10.3.0`. This tag will be dependent on the tag being used in your compose file for the associated image.

If you need to get shell access to a container, use `docker exec`:

```shell
docker exec -it <container id> sh
```

You can also run commands on a container directly using `docker exec`:

```shell
docker exec -it <app container id> php oil r install
```
