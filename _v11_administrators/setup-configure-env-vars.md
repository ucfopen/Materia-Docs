---
title: Setting up Server Variables
tagline: Using environment variables for configuration
class: admin
category: first_time
highlighter: yes
---

# Why Environment Variables?

Server variables are configured outside of the server application itself - why? Because direct modifications to configuration files in python would only persist for as long as that particular container is in service, unless you are using a private image with those changes baked in. That's difficult to recommend, because it would likely require committing sensitive information to a git repository, which is never a good idea.

With environment variables, we can ensure server configurations are present and persistent on the host machine and written to the containers by way of docker compose. You have a number of available options for where to write these environment variables. We recommend a `.env` file.

> Note: Make absolutely sure `DJANGO_ENV` is set to production when building your production instance.

## Where to Write Your Environment Variables

### Configure in docker compose

Environment variables can be defined within your docker compose file, instead of or alongside values defined in a `.env` file.

```ini
environment:
	- DJANGO_ENV=prod
	- MYSQL_ROOT_PASSWORD=my-root-password
	- MYSQL_DATABASE=materia
	- MYSQL_USER=materia_user
	- MYSQL_PASSWORD=my-materia-db-password
	- MYSQL_HOST=mysql
	- MYSQL_PORT=3306
env_file:
	- .env
```

If you choose to define environment variables within your compose file, we recommend only defining non-sensitive values. Sensitive values (such as passwords or hashes) are better defined in a `.env` file.

### Configure Using .env

This is the most common and recommended approach. We even provide a `.env.template` file in the `docker/` directory for you to use as a starting point.

> Note: Take extra care to make sure your .env file(s) are not accessible from the web.

## Environment Variables Reference

Let's review the `.env.template` file and walk through configurations.

```ini
# ==========================================================
# REQUIRED SETTINGS
# ==========================================================

# DB
MYSQL_HOST=
MYSQL_DATABASE=
MYSQL_USER=
MYSQL_PASSWORD=

# URLs
BASE_URL=
MEDIA_URL=
MEDIA_UPLOAD_URL=
WIDGET_URL=
STATIC_CROSSDOMAIN=

ENABLE_ADMIN_UPLOADER=true

# Session storage/cache driver
# ALLOWED VALUES: db | file | redis
SESSION_DRIVER=redis

# Asset Storage Driver
# ALLOWED VALUES: file | s3 | db
ASSET_STORAGE_DRIVER=file
```

These values are required and should be set at a minimum.

#### Database Configuration

```ini
MYSQL_HOST=
MYSQL_DATABASE=
MYSQL_USER=
MYSQL_PASSWORD=
```

Your database configuration values. `MYSQL_HOST` should not include the `mysql://` protocol. These values **must** be set.

#### URL Configuration

Generally, your URLs will conform to the values below:

```ini
BASE_URL="https://my-materia-instance.edu/"
MEDIA_URL="https://my-materia-instance.edu/media/"
MEDIA_UPLOAD_URL="https://my-materia-instance.edu/media/upload/"
WIDGET_URL="https://my-materia-instance.edu/widget/"
STATIC_CROSSDOMAIN="https://my-materia-instance.edu/"
```

#### Admin Uploader

```ini
ENABLE_ADMIN_UPLOADER=true
```

Enables the widget uploader GUI in the widget admin panel. This is safe to enable by default and is the easiest way to install or update widgets.

#### Session Driver

```ini
SESSION_DRIVER=redis
```

Configures the session driver. Allowed values are `db`, `file`, and `redis`. If using `redis`, a redis container must be configured to run alongside your `python` and `nginx` containers, or an exernal redis service (such as AWS Elasticache) can be used.

```ini
ASSET_STORAGE_DRIVER=s3
```

Configures the media asset storage driver. Allowed values are `s3`, `file`, and `db`. **We do not recommend using the database for asset storage**. If using AWS S3, additional configuration values must be provided; those are detailed below.

---


```ini
# ==========================================================
# CONDITIONALLY MANDATORY SETTINGS
# ==========================================================
# These settings are required depending driver selection.

# === SESSION DRIVER =======================================
# REQUIRED if SESSION_DRIVER=redis
REDIS_URL="redis://redis:6379/0"

# === ASSET STORAGE DRIVER =================================
# REQUIRED if ASSET_STORAGE_DRIVER=s3

# Where AWS credentials will be sourced
# env used for local development with fakes3. imds is highly recommended for production instances.
# ALLOWED VALUES: env | imds
ASSET_STORAGE_S3_CREDENTIAL_PROVIDER=env
ASSET_STORAGE_S3_REGION="us-east-1"
ASSET_STORAGE_S3_BUCKET=

# the most effective way to serve s3 assets is through a cloudfront distribution
# if enabled, the s3 driver will attempt to serve them from the domain provided
# if disabled, the s3 driver will serve assets via presigned urls from the bucket
# ALLOWED VALUES: true | false
ASSET_STORAGE_S3_USE_CDN=false
# CDN domain that asset paths are appended to. Must include protocol (https://)
ASSET_STORAGE_S3_CDN_DOMAIN=

# directory on disk to store original and resized assets
# this is effectively the subdir of the bucket where assets are hosted
ASSET_STORAGE_S3_BASEPATH="media"

# If using 'env' as credential provider, specify access key/secret here
AWS_ACCESS_KEY_ID=
ASSET_STORAGE_S3_SECRET=

# Set to false if using real S3 for local development
# ALLOWED VALUES: true | false
DEV_ONLY_USE_FAKES3=true
# URL of Fake S3 API endpoint when using Fake S3 for local development
# this is not used outside of fakes3
ASSET_STORAGE_S3_ENDPOINT="http://fakes3:10001"

```

#### Redis URL

```ini
REDIS_URL="redis://redis:6379/0"
```

This is only required if you are using `redis` as your asset storage driver.

#### AWS S3 Configurations

```ini
ASSET_STORAGE_S3_CREDENTIAL_PROVIDER=env
ASSET_STORAGE_S3_REGION="us-east-1"
ASSET_STORAGE_S3_BUCKET=
ASSET_STORAGE_S3_USE_CDN=false
ASSET_STORAGE_S3_CDN_DOMAIN=
ASSET_STORAGE_S3_BASEPATH="media"
AWS_ACCESS_KEY_ID=
ASSET_STORAGE_S3_SECRET=
DEV_ONLY_USE_FAKES3=true
ASSET_STORAGE_S3_ENDPOINT="http://fakes3:10001"
```

- `ASSET_STORAGE_S3_CREDENTIAL_PROVIDER`: determines the source for S3 credentials. If `imds` is available (such as when you're running Materia on an EC2), it is highly recommended you set this value to `imds`. Otherwise, use `env`.

- `ASSET_STORAGE_S3_REGION`: determines the region of your S3 bucket.
- `ASSET_STORAGE_S3_BUCKET`: the S3 bucket you will be using.
- `ASSET_STORAGE_S3_USE_CDN`: if enabled, a CDN URL will be used instead of an S3 bucket URL. We highly recommend this option, as it will reduce performance penalties from repeated S3 queries.
- `ASSET_STORAGE_S3_CDN_DOMAIN`: the CDN domain, if a CDN is in use. Typically a cloudfront URL.
- `ASSET_STORAGE_S3_BASEPATH`: the basepath within the bucket to store the media assets.
- `AWS_ACCESS_KEY_ID`: the AWS access key for S3 if `env` is selected as the credential provider.
- `ASSET_STORAGE_S3_SECRET`: the AWS secret value for the associated key if `env` is selected as the credential provider.
- `DEV_ONLY_USE_FAKES3`: toggles whether to use fakes3 in development. Enabled by default. Force-disabled if `DJANGO_ENV` is `prod`.
- `ASSET_STORAGE_S3_ENDPOINT`: the S3 endpoint. Used in conjunction with fakes3. Not required for S3 on AWS.

---

```ini
# ==========================================================
# LESS MANDATORY SETTINGS
# ==========================================================
# Not REQUIRED for the system to run

# === BASE URLS ============================================

# Override these if your static files aren't in ./staticfiles/dist/
# JS_BASEURL=
# CSS_BASEURL=
```

#### Static Asset Base URLs

```ini
JS_BASEURL=
CSS_BASEURL=
```

These only needs to be updated if the directory of your static assets is somewhere other than `./staticfiles/`.

---

```ini
# === LTI CONFIGURATION ====================================

# Where do we grab the institutional user ID from the LTI payload?
# If claim is an empty string, the identifier will be referenced at the top level
LTI_PAYLOAD_USER_CLAIM="https://purl.imsglobal.org/spec/lti/claim/lis"
LTI_PAYLOAD_USER_IDENTIFIER="person_sourcedid"

# AGS Scores Service potentially requires a different identifier than USER_IDENTIFIER for requests
# If set, these will be used in the specific context of AGS requests that require a userId
LTI_PAYLOAD_AGS_USER_CLAIM="https://purl.imsglobal.org/spec/lti/claim/custom"
LTI_PAYLOAD_AGS_USER_IDENTIFIER="user_canvas_id"
```

#### LTI user identifier

```ini
LTI_PAYLOAD_USER_CLAIM="https://purl.imsglobal.org/spec/lti/claim/lis"
LTI_PAYLOAD_USER_IDENTIFIER="person_sourcedid"
```

The location of the user identifier in the LTI payload. If `LTI_PAYLOAD_USER_CLAIM` is populated, the `LTI_PAYLOAD_USER_IDENTIFIER` will be referenced within the claim. Otherwise, the identifier will be referenced at the top level of the payload.

#### LTI AGS user identifier

```ini
LTI_PAYLOAD_AGS_USER_CLAIM="https://purl.imsglobal.org/spec/lti/claim/custom"
LTI_PAYLOAD_AGS_USER_IDENTIFIER="user_canvas_id"
```

AGS (Assignments and Grades Service) uses a different user identifier than the standard user claim/identifier defined above. If `LTI_PAYLOAD_AGS_USER_CLAIM` is not populated, the `LTI_PAYLOAD_AGS_USER_IDENTIFIER` will be referenced at the top level of the payload.

---


```ini
# === GENERATIVE AI ========================================

# ALLOWED VALUES: true | false
GENERATION_ENABLED=false
# ALLOWED VALUES: true | false
GENERATION_ALLOW_IMAGES=false
# ALLOWED VALUES: azure_openai | openai
GENERATION_API_PROVIDER=

GENERATION_API_KEY=
# If using Azure, use the base URL for your resource, trailing slash included.
GENERATION_API_ENDPOINT=
GENERATION_API_MODEL=
# ALLOWED VALUES: true | false
GENERATION_LOG_STATS=false
```

#### Generative AI Configurations

These determine whether or not generative AI features are enabled and where requests will be sent to.

- `GENERATION_ENABLED`: the master switch to enable or disable generative AI functionality.
- `GENERATION_ALLOW_IMAGES`: whether or not image generation is allowed in certain contexts. We do not recommend enabling this feature.
- `GENERATION_API_PROVIDER`: the provider for generative AI features. Currently `azure_openai` or `openai` are available as values.
- `GENERATION_API_KEY`: the API key for the AI generation provider.
- `GENERATION_API_ENDPOINT`: if Azure OpenAI is in use, this is typically the base URL of the resource. Include a trailing slash.
- `GENERATION_API_MODEL`: the model to use within the generative AI resource.
- `GENERATION_LOG_STATS`: enables verbose stats logging for generative AI requests. Defaults to `false`.

---

```ini
# === SYSTEM EMAILS ========================================

# ALLOWED VALUES: true | false
SEND_EMAILS=false
# what email address should system mails come from?
SYSTEM_EMAIL=""
EMAIL_BACKEND=""
# ^ To use an SMTP backend, use "django.core.mail.backends.smtp.EmailBackend" and fill in the SMTP env vars below.
#   To use a Sendgrid backend, use "sendgrid_backend.SendgridBackend" and fill in your Sendgrid API key in the env var below.
#   To use another backend, or for more info, see https://docs.djangoproject.com/en/5.2/topics/email/#topic-email-backends

# SMTP Backend Settings
EMAIL_HOST="localhost"
EMAIL_PORT=25
EMAIL_HOST_USER=""
EMAIL_HOST_PASSWORD=""
EMAIL_TIMEOUT=0

# Sendgrid Backend Settings
SENDGRID_API_KEY=""
```

#### System email configuration

Materia can be configured to send system emails when a user receives a notification, such as when a user been granted access to a widget or their access has been revoked.

- `SEND_EMAILS`: the master switch to determine whether or not the system sends emails.
- `SYSTEM_EMAIL`: the email address that will be used as the email sender.
- `EMAIL_BACKEND`: determines the email backend to use. Allowed values are `django.core.mail.backends.smtp.EmailBackend` (for Django SMTP) or `sendgrid_backend.SendgridBackend` for sendgrid.

#### SMTP email backend configuration

```ini
EMAIL_HOST="localhost"
EMAIL_PORT=25
EMAIL_HOST_USER=""
EMAIL_HOST_PASSWORD=""
EMAIL_TIMEOUT=0
```

These values are only required if using the `django.core.mail.backends.smtp.EmailBackend` backend option.

#### Sendgrid email backend configuration

```ini
SENDGRID_API_KEY=""
```

Populate the API key to use sendgrid.

---

#### Auth login overrides

```ini
# If using a non-standard authentication package or anything else that would
#  redirect login attempts, set this to the desired URL to redirect to from
#  "/login"
AUTH_LOGIN_ROUTE_OVERRIDE=false
RESTRICT_LOGINS_TO_LAUNCHES=false
```

These booleans override the default login behavior:

- `AUTH_LOGIN_ROUTE_OVERRIDE`: if set to a value other than `false`, visits to `/login` will redirect to the path provided. This is useful when making use of an external authentication service, such as an institutional single sign-on page. Materia will intelligently render an interstitial frame in some contexts with a "Login" button when appropriate, such as widget pre-embed views.
- `RESTRICT_LOGINS_TO_LAUNCHES`: effectively disables external or direct authentication options and requires users to authenticate through the LMS via LTI. This is ideal for "out of the box" Materia implementations. If `AUTH_LOGIN_ROUTE_OVERRIDE` is populated with a value other than `false`, the Materia login view will provide a redirect link using the URL configured in `AUTH_LOGIN_ROUTE_OVERRIDE`.

---

#### Sentry configuration

```ini
# Sentry
SENTRY_DSN=
SENTRY_ENVIRONMENT=development
```

Sentry is an effective tool for application insights. If you wish to use Sentry to ingest Materia events, populate the `SENTRY_DSN` and `SENTRY_ENVIRONMENT` values as appropriate.