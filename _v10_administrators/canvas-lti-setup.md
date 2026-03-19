---
title: Canvas LTI Setup
tagline: Getting Materia to work seamlessly with Canvas
class: admin
category: maintenance
---

# Installing Materia into Instructure's Canvas LMS

While Materia works perfectly well as a standalone application, integrating it with an LMS unleashes its full potential. This includes:

* Fast and automatic authentication with LTI
* Automatic grade passback when widgets are used as assignments

Materia currently supports Instructure's Canvas, but there's nothing stopping intrepid developers from building out support for additional Learning Management Systems.

## Configuration

Refer to the [Server Configuration Variables page](setup-configure-env-vars.html) for a detailed breakdown of all the LTI configuration options. As with other server variables, these configurations should ideally be defined as env variables in a `.env` or `.env.local`.

### Name Your Unique Materia Server (GUID)

Create a unique identifier for your Materia install.  In the context of LTI tools, this should be unique to your server.  Typically [reverse domain notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation) is used, converting your server's url `https://materia.ucf.edu` into to the value `edu.ucf.materia`.

This value should be represented via the `LTI_GUID` environment variable.

### Managing Consumers

When Materia receives an LTI launch, it looks at the `tool_consumer_info_product_family_code` sent by the consumer to determine which configuration to use.

Assuming an LTI launch is received using `tool_consumer_info_product_family_code` = `canvas`, Materia will search for the matching configuration by first checking for `lti.consumer.canvas`.  If it doesn't exist, the settings in `lti.consumer.default` will be used unless the `BOOL_LTI_GRACEFUL_CONFIG_FALLBACK` server variable is explicitly set to `false`.

### Multiple Consumers

Materia can support multiple consumer configurations, though it only ships with one by default: the `default` consumer. The LTI server variables are applied to this default consumer, as seen in the [lti.php config](https://github.com/ucfopen/Materia/blob/master/fuel/app/modules/lti/config/lti.php).

If different settings are needed for different LTI consumers, they can easily be defined by duplicating the contents of `lti.consumers.default` and creating a new consumer setting group.  For instance, Obojobo uses the family code of `obojobo-next`.  To define different settings for Obojobo, one would create `lti.consumers.obojobo-next` and populate it with the required settings.

For reference, a sample consumer for Obojobo Classic is commented out in [lti.php config](https://github.com/ucfopen/Materia/blob/master/fuel/app/modules/lti/config/lti.php) under `lti.consumers.obojobo`.

Note that adding additional consumers requires modification of the LTI config PHP file. This isn't as straightforward as changing the file on the `app` container filesystem, as these changes are ephemeral for the lifetime of the container. Modification of the LTI configuration file requires either:

1. Creating a custom variation of `lti.php` and volume-mounting it to `/var/www/html/fuel/app/modules/lti/config/lti.php` on the `app` container.
2. Publishing the modified `lti.php` file as part of a custom `app` image used in lieu of an official ucfopen `app` image.

For more information about volume mounting or publishing custom images, consult the [docker setup page](setup-configure-docker.html).

## Setting up Canvas

First, take a look at [Instructure's documentation](https://community.canvaslms.com/t5/Admin-Guide/tkb-p/admin#ExternalAppsLTI) which covers adding an external app to Canvas.

We usually use the "By URL" method, but you could also use "Paste XML" if desired.  The URL (and XML) you'll use should be visible at `https://<YOUR_MATERIA_URL>/lti` (view source for raw xml).

You'll enter the following settings into the **Add/Edit App** form:

* **Consumer Key**: Use the value assigned to the `LTI_KEY` server variable.
* **Shared Secret**: Use the value assigned to the `LTI_SECRET` server variable.
* **Configuration Type**: Select `By URL`.
* **Configuration URL**: Enter the url to your LTI configuration xml `https://<YOUR_MATERIA_URL>/lti`.

> It is **very important** you keep the shared secret... well, *secret*.

{% include figure.html
	no_thumb="true"
	url="admin/canvas-add-lti-app.png"
	alt="Screen shot of installing Materia into Instructure's Canvas External Tools"
%}
