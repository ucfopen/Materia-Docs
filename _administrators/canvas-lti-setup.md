---
title: Canvas LTI Setup
tagline: Getting Materia to work seamlessly with Canvas
class: admin
---

# Installing Materia into Instructure's Canvas LMS

Installing Materia LTI into Canvas adds some very nice-to-have integrations for students and faculty.  It allows scores to be passed back to your course grade book and students to log in and play seamlessly.

## Materia LTI Configuration

The first step is to make sure Materia's LTI is configured. The [initial Materia install task](administrative-commands.html#initial-materia-install-task) sets up a lot of these variables, but they can be edited manually.

**Do not edit** the example/base configuration is in `fuel/app/packages/modules/lti/config/lti.php`. Rather, edit or create an *environment* configuration file in `/fuel/app/packages/modules/lti/config/<ENVIRONMENT>/lti.php`.

> Fuelphp uses environments to determine which configuration options to load.  For more information view the <a href="http://fuelphp.com/docs/general/environments.html#/env_config">Fuelphp Documentation</a>.  The environment will typically be either `production` or `development`.

### Name Your Materia Install

Create a unique identifier for your Materia server install.  In the context of LTI tools, this should be unique to your server.  Typically [reverse domain notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation) is used, converting your server's url `https://materia.ucf.edu` into to the value `edu.ucf.materia`.

This identifier will be used for the value of `lti.tool_consumer_instance_guid` configuation setting.

### Add a Consumer

The configuration file defines which *consumers* can launch Materia widgets. For Canvas you need the following:

```php
<?php
// from /fuel/app/packages/modules/lti/config/<ENVIRONMENT>/lti.php

return [
	// Settings for this particular install
	// Change the tool_consumer_instance_guid to something unique to your install!
	'tool_consumer_info_product_family_code' => 'materia',
	'tool_consumer_instance_guid'            => '<YOUR_REVERSE_DOMAIN_IDENTIFIER>',

	'consumers' => [
		 // the consumer names must match the LTI launch param:
		 // 'tool_consumer_info_product_family_code'
		'canvas' => [
			// The LTI launch param to use for Materia's username
			'remote_username'       => 'lis_person_sourcedid',

			// When looking or creating local users based on the external
			// system what fields do we use as an identifier?
			// 'remote_identifier' is a variable from the lti launch the LMS sent
			// 'local_identifier' is the column to search in the user database
			// ex:
			// if 'local_identifier' is 'username'
			// and lti launch data lis_person_sourceid = 'dave'
			// materia will search the 'username' column for 'dave'
			//
			// Another option is to use email (remember email's change):
			// 'remote_identifier' = 'lis_person_contact_email_primary'
			// 'local_identifier' = 'email'
			// ex:
			// launch lis_person_contact_email_primary is 'me@mail.com'
			// materia searches the 'email' column for 'me@mail.com'
			'remote_identifier'     => 'lis_person_sourcedid',
			'local_identifier'      => 'username',

			// When true, materia will accept user data from the external system.
			// This means it will create users we don't have and update their user
			// data if it changes. It will NOT update any external roles
			// (see 'use_launch_roles')
			'creates_users'         => true,

			// allow an external system to define user roles in Materia
			'use_launch_roles'      => true,

			// which auth driver will do the final work authenticating this user
			// custom auth modules can be added to use SAML or lookup users in LDAP
			'auth_driver'           => 'Materiaauth',

			// Settings for the course navigation link
			// these affect the lti configuration xml
			// used by Canvas when installing Materia LTI
			'course_nav_enabled'    => 'true', // can it be turned on
			'course_nav_default'    => 'disabled', // is it turned on by default
			'course_nav_text'       => 'Materia', // what does the nav link say
			'course_nav_visibility' => 'admins', // who can see the nav link

			// Security Settings CHANGE THE SECRET (or both) !!!
			'secret'            => '<CHANGE_ME_NOW_SECRET>',
			'key'               => 'materia-production-lti-key',

		],
		//.. other consumers here
	]
];
```

## Setting up Canvas

First, take a look at [Instructure's tutorial](https://community.canvaslms.com/docs/DOC-3020) which covers adding an extension to Canvas. You'll enter the following settings into the **Edit External Tool** form:

* **Consumer Key**: Use a value from `lti.consumers.canvas.key` in the lti.php
* **Shared Secret**: Use the value from `lti.consumers.canvas.secret` in the  lti.php
* **Configuration Type**: Select `By URL`
* **Configuration URL**: Enter the url to your LTI configuration xml `https://<YOUR_MATERIA_URL>/lti`

> It is **very important** you keep the shared secret... well, *secret*.
