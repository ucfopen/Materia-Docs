---
title: Setting up Server Variables
tagline: Using environment variables for configuration
class: admin
category: first_time
highlighter: yes
---

# Why Environment Variables?

Server variables are configured outside of the server application itself - why? Because direct modifications to configuration files in PHP would only persist for as long as that particular container is in service, unless you are using a private image with those changes baked in. That's difficult to recommend, because it would likely require committing sensitive information to a git repository, which is never a good idea.

With environment variables, we can ensure server configurations are present and persistent on the host machine and written to the containers by way of docker compose. You have a number of available options for where to write these environment variables. We recommend a `.env` or `.env.local` file.

> Note: It is crucial that you don't expose phpinfo() pages to the web. It will display your secrets! In development mode, Materia exposes `/dev/php-info`! Make absolutely sure `FUEL_ENV` is set to production when building your production instance.

# Where to Write Your Environment Variables

## Configure Using .env

This is the most common and recommended approach. If you cloned Materia and used the nondev setup script, preconfigured environment variables will be present in `docker/.env`, and custom values written to `.env.local`. In a production environment, we recommend using a single `.env` file.

In the Materia repository, the root `.env` file can serve as a template for your own copy of `.env`.

Regardless of your `.env` file naming scheme, ensure your docker compose file imports them appropriately:

```ini
app:
  env_file:
    - .env
```

Multiple .env files can be referenced as well. Values in later imports take precedence:

```ini
app:
  env_file:
    - .env
	- .env.local
```

> Note: Take extra care to make sure your .env file(s) are not accessible from the web.

## Configure Using NGINX

You can set php environment variables in your NGINX config using the `fastcgi_param` option.

```nginx
location ~ ^/index.php$ {
    fastcgi_pass  127.0.0.1:9000;
    #... clip ...

    # HERE!
    fastcgi_param FUEL_ENV development;

    # OR in this file
    include fastcgi_params;
}
```

# Environment Variables Reference

The config key below shows all of the available environment variables.

> Note: only `BOOL_` options become boolean values. And ONLY `true` evaluates to true.

---

```ini
FUEL_ENV=production
```

**String**. **Must be set**. Sets the FuelPHP environment. Options are `development` or `production`. In any public-facing instance of Materia, this _must_ be set to `production`.

---

```ini
MYSQL_USER=
MYSQL_PASSWORD=
MYSQL_DATABASE=materia
DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@mysql/${MYSQL_DATABASE}
```

**String**(s). `DATABASE_URL` **must be set**. `DATABASE_URL` is the most important value here: `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_DATABASE` are not explicitly referenced by the application but can be used to populate the value of `DATABASE_URL` using the syntax provided above. Alternatively, you can use `DATABASE_URL` exclusively with hard-coded username, password, and database values.

Note that in instances where a mysql container is in service, the `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_DATABASE` values should be provided. **We do not recommend using a mysql container in production instances**.

---

```ini
BOOL_SEND_EMAILS=false
```

**Boolean.** Determines whether the system should send email notifications when users share, modify, or revoke widget access with other users.

---

```ini
SYSTEM_EMAIL=
```
**String**. **Must be set**. Sets the system email (where email notifications will be sent from).

---

```ini
FUEL_LOCAL=en_US.UTF-8
```

**String**. Sets PHP localization. Defaults to `en_US.UTF-8` if not explicitly set here.

---

```ini
FUEL_ALWAYS_LOAD_PACKAGES="orm,auth,materiaauth,ltiauth"
```

**String. Comma-delineated values.** Tells FuelPHP what module packages to load. Defaults to `orm,auth,materiaauth,ltiauth` if unset. Two instances where you may be using additional module packages:

1. You are using a custom authentication module.
2. You are using a custom theme module.

---

```ini
FUEL_ALWAYS_LOAD_MODULES=""
```

**String. Comma-delineated values.** Tells FuelPHP what modules to load. You will likely be using the packages configuration instead; this value is not generally used.

---

```ini
GOOGLE_ANALYTICS_ID=
```

**String. Hash.** If you have a google analytics ID, you can enter it here.

---

```ini
FUEL_LOG_THRESHOLD=300
```

**Integer**. Sets the logging threshold for FuelPHP. Refer to FuelPHP's [Log Constants](https://fuelphp.com/docs/classes/fuel.html#log_constants) configuration documentation.

---

```ini
LOG_HANDLER=DEFAULT
```

**String. Constant.** Defines a custom log handler. This is an advanced FuelPHP configuration. Default is `DEFAULT`; this will write logs to `fuel/app/logs/`. `STDOUT` is preferable for development to ensure logs are captured in the compose log stream.

---

```ini
URLS_STATIC=
URLS_ENGINES=
```

**String**(s). Defines the values used to construct asset URL paths. For more information about how to configure these, visit [domain name requirements](domain-name-requirements.html).

---

```ini
BOOL_ADMIN_UPLOADER_ENABLE=true
```

**Boolean**. Determines whether the widget admin panel UI allows widget engine file uploads. The widget admin panel is only visible to users with the `super_user` role. We generally recommend using a single dedicated service user with `super_user`. Widget uploads will always be available through PHP oil commands in the shell. Defaults to `true`.

---

```ini
ASSET_STORAGE_DRIVER=file
```

**String**. Specifies the FuelPHP storage driver for assets. Options are `file`, `s3`, or `db`. Defaults to `file`.

Specifies the method of asset storage for your instance of Materia. The vast majority of users will want `file`, which stores user-uploaded media in the filesystem of the application container and, if properly configured, this media directory is volume-mounted from the host machine.

We provide `s3` as an asset storage solution for users with the ability to leverage infrastructure on AWS. While our implementation is S3-specific, it should be feasible to create analogous storage driver solutions for other cloud hosting providers. Note that proper configuration of S3 storage requires a number of additional configuration values to be set, which are detailed below. When the S3 storage driver is in use, media urls will continue to utilize the static asset URLs as defined: the server application will perform the requests to AWS S3 directly, create the image file in memory, and serve it to the client.

The `db` asset storage driver instead stores asset binaries within the database's `asset_data` table. This storage driver solution is primarily meant for use with low-cost cloud hosting solutions with minimal disk space allotments. **It is not intended for use at scale**.

> We strongly advise against using the `db` storage driver solution. It will be completely removed in the next major version of Materia!

---

```ini
ASSET_STORAGE_S3_CREDENTIAL_PROVIDER=
ASSET_STORAGE_S3_BUCKET=
ASSET_STORAGE_S3_ENDPOINT=
ASSET_STORAGE_S3_KEY=
ASSET_STORAGE_S3_SECRET=
ASSET_STORAGE_S3_REGION=
ASSET_STORAGE_S3_BASEPATH=
AWS_SESSION_TOKEN=
```

**Strings**. These values are only required if your `ASSET_STORAGE_DRIVER` configuration is set to `s3`. Let's run through each.

* `ASSET_STORAGE_S3_CREDENTIAL_PROVIDER`: **Must be set**. values must be `env` or `imds`. This determines where your S3 authentication credentials are sourced from. We recommend `imds`, the Amazon [instance metadata service](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html). Notably, this option is only available when your instance is running on an EC2 container. `imds` has several advantages, being much easier to setup, and avoids the complexity associated with rotating session token values.

* `ASSET_STORAGE_S3_BUCKET`: **Must be set**. The name of your S3 bucket.
* `ASSET_STORAGE_S3_ENDPOINT`: This value is _not_ required for S3 on AWS. It is used in local development when the `fakes3` container is in service, which emulates S3 for use with the S3 asset storage driver.
* `ASSET_STORAGE_S3_KEY`: S3 authentication key. Required when using `env` for your credential provider. Not required with `imds`.
* `ASSET_STORAGE_S3_SECRET`: S3 authentication secret. Required when using `env` for your credential provider. Not required with `imds`.
* `ASSET_STORAGE_S3_REGION`: The region of your S3 bucket. Defaults to `us-east-1` if not set.
* `ASSET_STORAGE_S3_BASEPATH`: The basepath for asset storage within your bucket. Defaults to `media` if not set.
* `AWS_SESSION_TOKEN`: Your AWS session token for S3 authentication. Required when using `env` for your credential provider. Not required with `imds`. We do not provide an out-of-the-box solution to manage token rotation.

---

```ini
CACHE_DRIVER=file
```

**String**. Sets the server cache driver. Options are `file` or `memcached`. Defaults to `file` if not set. If using `memcached`, additional configuration values are required, as detailed below.

---

```ini
MEMCACHED_HOST=localhost
MEMCACHED_PORT=11211
```

Used when `CACHE_DRIVER` is `memcached`:

* `MEMCACHED_HOST`: **String**. Defaults to `localhost` if not set.
* `MEMCACHED_PORT`: **Integer**. Defaults to `11211` if not set.

Note that our recommended implementation of memcached uses a dedicated docker container alongside the `app` and `webserver` containers. Additions to your docker compose file are required.

---

```ini
SESSION_DRIVER=file
```

**String**. Sets the session driver for the Materia server application. Acceptable values are `memcached`, `file`, or `db`. Defaults to `file` if not set. Using `memcached` requires additions to your docker compose file.

---

```ini
SESSION_EXPIRATION=21600
```

**Integer**. Sets the expiration of server sessions. Defaults to `21600` if not set.

---

```ini
THEME_ACTIVE=default
THEME_PACKAGE=materia-theme-ucf
```

**Strings**. Sets the FuelPHP theme. Themes allow for deep customization of pages in FuelPHP.

> Theming in FuelPHP became considerably more complex after Materia v10, as page content is entirely rendered on the client. We acknowledge that theming is not ideal currently, and will be working to make it easier to extend and customize in the future.

---

```ini
AUTH_DRIVERS=Materiaauth
```

**String**. Sets the auth driver for the application. Defaults to `Materiaauth` if not set. The built-in Materia auth is sufficient for the creation and management of service user accounts; it is insufficient for user management at scale. Users may extend the capability of `Materiaauth` by prepending authentication through an external service such as SAML. In such cases, successful authentications can then provide various attributes from a SAML payload which are then used to create or update records in the database. Bear in mind we do not provide this sort of authentication solution out of the box.

---

```ini
AUTH_SALT=
AUTH_SIMPLEAUTH_SALT=
```

**Strings**. **Hashes**. **Must be set**. These are the salt values used for password authentication. **These values must be properly generated and unique**.

You can use the following php shell command to generate valid salt hashes locally. This can be performed within the `app` container shell environment:

```shell
php -r "echo(sodium_bin2hex(random_bytes(SODIUM_CRYPTO_STREAM_KEYBYTES)));"
```

---

```ini
USER_SYSTEM_PASSWORD=
USER_INSTRUCTOR_PASSWORD=
USER_STUDENT_PASSWORD=
```

**Strings**. Configure the password for the default system users (`~materia_system_only`, `~author`, `~student`). These are only used if the `create_default_users` FuelPHP oil task is run, which is intended for development. These accounts will not be generated in a production environment unless the oil task is run manually. Generally speaking, we do not advise you run this task in production. Create your own service user accounts manually instead.

---

```ini
CIPHER_KEY=
CRYPTO_KEY=
CRYPTO_IV=
CRYPTO_HMAC=
```

**Strings**. **Hashes**. Sets the cryptographic keys used for authentication. `CIPHER_KEY` is used for contemporary cryptography with Sodium; the `CRYPTO_` values are used for legacy cryptography with PHPSecLib. Consult the [FuelPHP Crypt class config](https://fuelphp.com/docs/classes/crypt/config.html) documentation for more info.

---

```ini
LTI_KEY=
LTI_SECRET=
LTI_GUID=
LTI_TOOL_ID=
LTI_REMOTE_USERNAME=
LTI_REMOTE_IDENTIFIER=
```

**Various**. LTI configuration settings which are used in conjunction with your LMS. Only `LTI_KEY` and `LTI_SECRET` are _required_. You will provide these values in the LMS when adding a new external application. More information is available in our [Canvas LTI Setup](canvas-lti-setup.html) page.

* `LTI_KEY`: The LTI authentication key for Materia. This does not need to be a unique hash, but a human-readable string such as `"materia-production-key"`.
* `LTI_SECRET`: The LTI authentication secret. This should be a secure, unique hash. Do not share it with anyone.
* `LTI_GUID`: The `tool_consumer_instance_guid` field of the LTI payload. We recommend something unique, preferably using the [reverse domain notation](https://en.wikipedia.org/wiki/Reverse_domain_name_notation) of your Materia instance.
* `LTI_TOOL_ID`: Sets the `tool_id` field of your LTI config. It is generally acceptable for this to be the same value as `LTI_GUID`.
* `LTI_REMOTE_USERNAME`: Determines the field of the LTI payload to use for username reference. Defaults to `lis_person_sourcedid` if not set.
* `LTI_REMOTE_IDENTIFIER`: Remote user identifier to reference for LTI authentication. May be paired with a local identifier (which is by default set to `username`).

---

```ini
BOOL_LTI_RESTRICT_LOGINS_TO_LAUNCHES=false
BOOL_LTI_COURSE_NAV_DEFAULT=false
BOOL_LTI_CREATE_USERS=true
BOOL_LTI_USE_LAUNCH_ROLES=true
BOOL_LTI_GRACEFUL_CONFIG_FALLBACK=true
BOOL_LTI_LOG_FOR_DEBUGGING=false
```

**Booleans**. These are additional configuration toggles to manage various aspects of LTI integration:

* `BOOL_LTI_RESTRICT_LOGINS_TO_LAUNCHES`: Prevents direct logins. Users will _only_ be able to authenticate with Materia through LTI launches. This is desireable when you are deferring to an LMS for authentication, as direct logins are incompatible with these user records unless authentication is handled by a common third party service such as SAML or Entra ID with an accompanying authentication module. Note that service users (users who are created manually using oil commands) can continue to bypass this restriction for logins by providing `?directlogin=1` as a URL parameter to the login URL: `materia.myinstitution.edu/login?directlogin=1`. Defaults to `false`.
* `BOOL_LTI_COURSE_NAV_DEFAULT`: Tells Canvas whether to include Materia in course navigation by default. Defaults to `false`.
* `BOOL_LTI_CREATE_USERS`: Instructs Materia as to whether to allow new user records in the Materia database via LTI authentication. This is preferable for most use cases with an LMS. If a user authenticates with Materia through an LTI launch, Materia will create a new record for that user if one is not present when this option is enabled. Defaults to `true`.
* `BOOL_LTI_USE_LAUNCH_ROLES`: Instructs Materia to defer role management to LTI launches. For example, if a launch role includes `Instructor`, Materia will provision the `basic_author` permission level. Conversely, if a launch role indicates they are a student, Materia will revoke the `basic_author` role. User roles will be based on the role provided by their most recent LTI launch. Defaults to `true`.
* `BOOL_LTI_GRACEFUL_CONFIG_FALLBACK`: Allows Materia to fall back to the default consumer configuration if a consumer is not recognized. Defaults to `true`.
* `BOOL_LTI_LOG_FOR_DEBUGGING`: Instructs Materia to include additional LTI launch information in logs for debugging purposes. Defaults to `false`.

---

```ini
GENERATION_ENABLED=false
GENERATION_ALLOW_IMAGES=false
GENERATION_API_PROVIDER=
GENERATION_API_KEY=
GENERATION_API_ENDPOINT=
GENERATION_API_VERSION=
GENERATION_API_MODEL=
GENERATION_LOG_STATS=true
```

**Various**. With version 10.3, Materia added support for the use of generative AI in limited and controlled situations associated with widget instance content authoring. We use the [OpenAI PHP client](https://github.com/openai-php/client) to facilitate these queries. The client has support for two implementations: the OpenAI platform API, and Azure OpenAI. **Be mindful that there are additional costs incurred with use of this feature**. Not all configuration options are required for both implementations. Let's review each:

* `GENERATION_ENABLED`: the primary toggle to tell Materia whether to enable generative AI features. Note that even if this option is `true`, if the OpenAI client fails to recognize valid configuration values, the feature will not be enabled. Defaults to `false`.
* `GENERATION_ALLOW_IMAGES`: Allows for generation of images. Support for this option is limited. **We do not recommend enabling this option**: image generation is extremely expensive at scale. Defaults to `false`.
* `GENERATION_API_PROVIDER`: **Must be set**. Sets the API provider. Must be `openai` or `azure_openai`.
* `GENERATION_API_KEY`: **Must be set**. The API key used for authentication with the API provider.
* `GENERATION_API_ENDPOINT`: Required for `azure_openai`. Should represent a URL in a format similar to the following: `https://my-resource-or-deployment.openai.azure.com/openai/deployments/gpt-4o-mini`.
* `GENERATION_API_VERSION`: Required for `azure_openai`. This may be a parameter as part of the original resource URL, ex: `2023-03-15-preview`.
* `GENERATION_API_MODEL`: Required for `openai`.
* `GENERATION_LOG_STATS`: Determines whether stats associated with requests to the API (tokens, etc) are logged. Logs are set to the debug threshold. Defaults to `true`.

---

```ini
IS_SERVER_HTTPS=true
```

**Boolean**. Tells FuelPHP whether to manage cookies securely. There are very few reasons why you'd want to set this to false, but it's available as an option if desired. Defaults to `true`.
