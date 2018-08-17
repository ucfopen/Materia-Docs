---
title: Domain Name Requirements
tagline: For development &amp; production
class: admin
category: server
---

# Domain Setup

Materia is designed to run from **two domains** for security and speed.  The main domain is where the php application is running, and one for the static assets like widgets, javascript, and css.

This separation is important to create a browser restricted sandbox for the widgets to run inside. It keeps widget code (not to mention user content) from having script access to the main application except through a restricted api.

We know in some environments that this may be tedious, so it is possible to use a single domain.

> We highly reccomend using 2 domains

## Two Domains with a CDN (Best)

You'll need to set up a CDN, but users will get super fast downloads and your widgets will be properly sandboxed.

> Your Apache/Nginx config will only have to listen to one domain.  The CDN should be configured to load uncached assets directly from your app server.

```php
<?php
return [
	'urls' => [
		// where is the css/js
		'static' => 'https://fff.mycdn.com/',
		 // where are the widget files
		'engines'  => 'https://fff.mycdn.com/widgets/',
	]
];
```

## Two Domains (Recommended)

This is how we recommend running in production.

> Two domains will require some custom Apache/Nginx configuration. The 'static files' section in the [Apache Virtual Host Example](apache-virtual-host-configuration.html) would need to be configured to respond to the second domain.

```php
<?php
return [
	'urls' => [
		// where is the css/js
		'static' => 'https://static.mymateria.school.edu/',
		 // where are the widget files
		'engines'  => 'https://static.mymateria.school.edu/widgets/',
	]
];
```

## One Domain, different Ports (Not Bad)

You can still initiate sandboxing by using a non-standard port on the same domain. Here, we'll use port 8080.

> Two domains will require some custom Apache/Nginx configuration. The 'static files' section in the [Apache Virtual Host Example](apache-virtual-host-configuration.html) would need to be configured to respond to the port you choose.

```php
<?php
return [
	'urls' => [
		// where is the css/js
		'static' => 'https://mymateria.school.edu:8080/',
		 // where are the widget files
		'engines'  => 'https://mymateria.school.edu:8080/widgets/',
	]
];
```

## One Domain (Not Recommended)

This is the easiest setup, just using one domain to host everything.  It's not wise, but it will work.


```php
<?php
return [
	'urls' => [
		// where is the css/js
		'static' => 'https://mymateria.school.edu/',
		 // where are the widget files
		'engines'  => 'https://mymateria.school.edu/widgets/',
	]
];
```
