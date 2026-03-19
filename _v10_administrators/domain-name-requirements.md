---
title: Setting up Domains
tagline: For development &amp; production
class: admin
category: first_time
---

# Domain Setup

Materia is capable of running from **two domains** for security and speed.  The main domain is where the PHP application is running, and one for the static assets like widgets, javascript, and css.

This separation is important to create a browser restricted sandbox for the widgets to run inside. It keeps widget code (not to mention user content) from having script access to the main application except through a restricted api.

We know in some environments that this may be tedious, so it is possible to use a single domain.

## Two Domains with a CDN (Best)

You'll need to set up a CDN, but users will get super fast downloads and your widgets will be properly sandboxed.

> Your NGINX config will only have to listen to one domain.  The CDN should be configured to load uncached assets directly from your app server.

**In Your environment variables:**
```bash
URLS_STATIC=https://fff.mycdn.com/
URLS_ENGINES=https://fff.mycdn.com/widgets/
```

## Two Domains (Recommended)

This is how we recommend running in production. Make sure your NGINX configuration is listening on both domains. See the [NGINX Configuration](nginx-configuration.html) page for more info.

**In Your environment variables:**
```bash
URLS_STATIC=https://static.mymateria.school.edu/
URLS_ENGINES=https://static.mymateria.school.edu/widgets/
```

## One Domain, Different Ports (Not Bad)

You can still initiate sandboxing by using a non-standard port on the same domain. Here, we'll use port 8080. Like the two-domain setup, your NGINX configuration will need to listen on both ports. Additionally, the `webserver` service definition in your compose file should include both ports.

**In Your environment variables:**
```bash
URLS_STATIC=https://mymateria.school.edu:8080/
URLS_ENGINES=https://mymateria.school.edu:8080/widgets/
```

**In your docker compose config:**
```ini
services:
  webserver:
    ports:
	  - "80:80"      # main application
	  - "8080:8080"  # static assets & engine files
```

## One Domain (Not Recommended)

This is the easiest setup, using just one domain to host everything. It's not wise, but it will work.

**In Your environment variables:**
```bash
# URLS_STATIC=		# ensure URLS_STATIC is unset
# URLS_ENGINES=		# ensure URLS_ENGINES is unset
```
