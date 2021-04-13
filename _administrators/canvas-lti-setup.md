---
title: Canvas LTI Setup
tagline: Getting Materia to work seamlessly with Canvas
class: admin
category: administrative
---

# Installing Materia into Instructure's Canvas LMS

Installing Materia LTI into Canvas adds some very nice-to-have integrations for students and faculty.  It allows scores to be passed back to your course grade book and students to log in and play seamlessly.

## Heroku LTI Configuration

Using the "Deploy to Heroku" button causes Heroku to generate a random secret for you. In most cases you simply need to locate those in the Heroku app dashboard. The `LTI_SECRET` and `LTI_KEY` values will be needed when you enter them into Canvas's LTI App installation.

## ENV Variables Configuration (v8.0.0 and newer)

Materia [v8.0.0](https://github.com/ucfopen/Materia/releases/tag/v8.0.0) added a simplified configuration. The common LTI settings can be set in `.env.local` or by using [Environment variables](https://github.com/ucfopen/Materia#configuring).


## Advanced Configuration (all versions)

Use the advanced method if:
* Environment variables are not provided for the setting you need to change.
* A custom configuration is desired when connecting multiple consumers.
* You are using Materia v7.0.1 or older, which don't support env configuration.


### FuelPHP Configuration Info

FuelPHP uses environments to determine which configuration options to load.  For more information view the [Fuelphp Documentation](http://fuelphp.com/docs/general/environments.html#/env_config).  The environment should be `production` for any server.

Assuming `FUEL_ENV` is set to production, FuelPHP will load and merge the following in order:
1. fuel/app/packages/modules/lti/config/lti.php
2. fuel/app/packages/modules/lti/config/production/lti.php

You can edit the base config or the environment config as needed by your use case.

> Heroku is a special case.  To update a configuration file on Heroku, you must save those changes and re-deploy your app. Manually changing files by editing them on the running app will not persist.  If available, it is advisable to use env vars on Heroku.

### Name Your Unique Materia Server (GUID)

Create a unique identifier for your Materia install.  In the context of LTI tools, this should be unique to your server.  Typically [reverse domain notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation) is used, converting your server's url `https://materia.ucf.edu` into to the value `edu.ucf.materia`.

Your GUID can be set in either:
* Environment variable / .env.local: `LTI_GUID`
* Manual config in lti.php: `lti.tool_consumer_instance_guid`

### Add a Consumer

When Materia receives an LTI launch, it looks at the `tool_consumer_info_product_family_code` sent by the consumer to determine which configuration to use.

Assuming an LTI launch is received using `tool_consumer_info_product_family_code` = `canvas`, Materia will search for the matching configuration by first checking for `lti.consumer.canvas`.  If it doesn't exist, the settings in `lti.consumer.default` will be used.  Prior to v8.0.0, the default fallback doesn't occur.

### Default Consumer (Materia v8.0.0 and newer)

The `default` consumer is the easiest way to set common LTI settings for all LTI consumers.  Changing a setting here will apply to all consumers that don't match a specifically configured consumer.

For reference, the default consumer is defined in the [lti.php config](https://github.com/ucfopen/Materia/blob/master/fuel/app/modules/lti/config/lti.php) under `lti.consumers.default`.

### Multiple Consumers

If different settings are needed for different LTI consumers, they can easily be defined by duplicating the contents of `lti.consumers.default` and creating a new consumer setting group.  For instance, Obojobo uses the family code of `obojobo-next`.  To define different settings for Obojobo, one would create `lti.consumers.obojobo-next` and populate it with the required settings.

For reference, a sample consumer for Obojobo Classic is commented out in [lti.php config](https://github.com/ucfopen/Materia/blob/master/fuel/app/modules/lti/config/lti.php) under `lti.consumers.obojobo`.

## Setting up Canvas

First, take a look at [Instructure's tutorial](https://community.canvaslms.com/docs/DOC-3020) which covers adding an extension to Canvas.

We usually use the "By URL" method, but you could also use "Paste XML" if desired.  The URL (and XML) you'll use should be visible at `https://<YOUR_MATERIA_URL>/lti` (view source for raw xml).

You'll enter the following settings into the **Add/Edit App** form:

* **Consumer Key**: Use a value from `lti.consumers.default.key` in the lti.php
* **Shared Secret**: Use the value from `lti.consumers.default.secret` in the  lti.php
* **Configuration Type**: Select `By URL`
* **Configuration URL**: Enter the url to your LTI configuration xml `https://<YOUR_MATERIA_URL>/lti`

> It is **very important** you keep the shared secret... well, *secret*.

{% include figure.html
	no_thumb="true"
	url="admin/canvas-add-lti-app.png"
	alt="Screen shot of installing Materia into Instructure's Canvas External Tools"
%}
