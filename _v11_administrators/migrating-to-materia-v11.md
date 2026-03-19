---
title: Migrating to Python Materia
tagline: Transitioning from PHP to python
class: admin
category: v11_upgrade
---

# Before You Begin

Transitioning to Materia v11 is a bit of an undertaking, and we **strongly** recommend working out a game plan in advance of any action. Your migration process will look something like this:

1. Back up all essential files: your env vars, the user-uploaded media directory, your docker compose file, your widget engine directory, and any other custom configuration files, such as NGINX.
2. Disable traffic to your Materia instance. Because of the database migration, you do not want to deal with new DB writes while performing the transition. **Plan for several hours of downtime**, though the length of this process is largely dictated by the size of your database.
3. Perform a final backup of your PHP Materia database.
4. Update your server configurations, whether those are in your `docker-compose.yml` file, in an `.env` file, or both. Additionlly, update or otherwise have your new `docker-compose.yml` file ready to go.
5. Initialize the Materia v11+ docker containers.
6. Initialize the database migration.
7. Perform post-migration cleanup tasks and restore traffic to Materia.
8. Reinstall widgets.
9. Install Materia as a LTI 1.3 integration in your LMS.

Let's go over these steps in more detail.

## Backup Essential Files

Review the file contents of your host machine, whether that's a dedicated server or a server instance in the cloud, like AWS EC2. Review the **Host Machine Files & Permissions** section of our [Docker Setup](setup-configure-docker.html) page if you need a refresher.

In particular, you will want to pay extra attention to user-uploaded media and widget engine files, since these are retained across Materia versions. Your user-uploaded media location will depend on the asset storage driver in use by PHP Materia: for `file` drivers, they are likely located in a top-level `media` directory that is volume-mounted into the application and webserver containers. Your widget engine files are likely located in a top-level `widget` directory that is similarly volume-mounted.

If you are using `s3` or `db` asset storage drivers, you don't have to worry about user media files on the host machine: just ensure your S3 configurations are correct in your new `.env` file.

## Disable Traffic to Materia

For the duration of the migration process, you will want to disable traffic to your Materia instance. Primarily, this is to prevent writes to the DB from occurring while the DB migration is in progress. Ensure a communication plan is in place to ensure downtime is minimally disruptive.

## Perform a Final Database Backup

Once traffic has ceased, perform a final database backup. The PHP to python migration is a one-way trip for a given copy of the database.

## Update Server Configurations

Review the [Setting up Server Variables](setup-configure-env-vars.html) page in advance and populate a new or existing `.env` file with the appropriate values. Many are 1:1 with their PHP counterparts, but pay special attention to new additions, like the LTI user claims. If you choose to store environment variables directly in your `docker-compose.yml` file, we recommend only storing non-sensitive values there.

## Update Your Docker Compose File

You can prepare the new `docker-compose.yml` file in advance as well. Crucially, the `app` and `webserver` services are being renamed to `python` and `nginx`, and the path to the images has changed too. Volume mounts can be remapped fairly easily from the old services to the new ones.

> Materia uses gunicorn as its WSGI server application. You can edit the workers and threads in the gunicorn command to accommodate your server CPU and load expectations.

## Initialize the New Docker Containers

When your compose file and configuration files are prepped on the host machine, you can start the new containers via:

`docker compose up -d`

Shell access for the new containers works the same way as the PHP containers. Use `docker exec` to get in to the python container. You can use the container name or ID to get in:

```shell
$ docker exec -it materia-django-python-1 sh
```

or:

```shell
$ docker exec -it <python container id> sh
```

## Begin the Database Migration

First, some context: our team engineered the migrations in Django to treat the PHP Materia database schema as the "starting point" of the database, then apply Django-specific changes on top. Migration `0001`'s primary purpose is to create this initial starting condition: it generates functional equivalents to the state of the database when used with FuelPHP, though not identical ones. Django is more strict about data integrity and expects certain schema conventions to be followed that are not present in the FuelPHP database.

In cases where an instance of Materia is transitioning from PHP to python, the changes in migration `0001` are not required since we can assume the database is at that point already. Therefore, we have to tell Django that the migration has been performed, then proceed to migration `0002` through the remainder.

This is done with a Django management command: `fake_current_db`. This command serves two primary purposes:

1. It tells Django that migration `0001` has been performed.
2. It performs certain preflight operations to ensure the database schema is acceptable to Django for the following migration steps.

Once in the python container shell, you can proceed with the database migration process.

First, run:
```python
$ python manage.py fake_current_db
```

When this operation is complete, you can proceed to run to full suite of migrations either all at once or one-at-a-time:

```python
$ python manage.py migrate
````

Alternatively, migrations can be run sequentially by specifying them to Django:

```python
$ python manage.py migrate core 0002
```

Note that there are additional migrations that must be run in addition to those in `core`. You can follow-up at any point with `python manage.py migrate` to ensure the full suite of migrations is performed.

**Be prepared to wait a while.** These operations are potentially significant depending on the volume of data in your database.

## Perform Post-Migration Cleanup Tasks

#### Update superuser password(s)

Take a deep breath. The scary part is over! Now we have a few cleanup items to address. Primarily, you will need to update the passwords of any service users, such as superusers, because the salt hash and cryptographic methods used by Django (you remembered to set `DJANGO_SECRET_KEY`, right?) are different from that used by FuelPHP.

```shell
$ python manage.py changepassword <superuser username>
```

Do this for any other service accounts you care about.

#### Manually update superuser database fields

This next step will require direct access to your database, using a tool like Sequel Ace or even mysql cli. Visit the `auth_user` table and flip `is_staff` and `is_superuser` to `1` for the superuser account(s).

## Update Widgets

Now that Materia itself has been updated, you'll need to ensure the widget catalog is updated alongside. This is because widgets contain a single bit of server-side code: the score module. All public UCF widgets contain python score modules, and the ones installed in your instance of Materia must be updated to include them. As a bonus, as of Materia v11, all public widgets can be easily managed and updated in the future using our new widget management tools in the widget admin page.

You can update the widget catalog in one go with another Django management command:

```shell
$ python manage.py post-install sync_widgets
```

This command uses a default manifest of public widgets. You can optionally pass a JSON file containing a list of just the widgets you'd like to update as an argument to the command:

```json
target_widgets = {
	"adventure": "ucfopen/adventure-materia-widget",
	"crossword": "ucfopen/crossword-materia-widget",
	"enigma": "ucfopen/enigma-materia-widget",
	"equation-sandbox": "ucfopen/equation-sandbox-materia-widget",
	"flash-cards": "ucfopen/flash-cards-materia-widget",
	"guess-the-phrase": "ucfopen/guess-the-phrase-materia-widget",
	"labeling": "ucfopen/labeling-materia-widget",
	"last-chance-cadet": "ucfopen/last-chance-cadet-materia-widget",
	"matching": "ucfopen/matching-materia-widget",
	"normal-distribution-calculator": "ucfopen/normal-distribution-calculator-materia-widget",
	"radar-grapher": "ucfopen/radar-grapher-materia-widget",
	"secret-spreadsheet": "ucfopen/secret-spreadsheet-materia-widget",
	"sequencer": "ucfopen/sequencer-materia-widget",
	"simple-survey": "ucfopen/survey-materia-widget",
	"slope-finder": "ucfopen/slope-finder-materia-widget",
	"sort-it-out": "ucfopen/sort-it-out-materia-widget",
	"syntax-sorter": "ucfopen/syntax-sorter-materia-widget",
	"this-or-that": "ucfopen/this-or-that-materia-widget",
	"word-search": "ucfopen/word-search-materia-widget",
	"be-finder": "ucfopen/be-finder-materia-widget",
	"word-guess": "ucfopen/word-guess-materia-widget",
}
```

via:

```shell
$ python manage.py post-install sync_widgets widget_manifest.json
```

## Restore Traffic to Materia

Now it's time to make Materia accessible to the outside world again. Once you restore traffic, make sure to visit your Materia instance and make sure everything looks good. You can continue to login as a service user by visiting `/login?directlogin=1`.

## Install the LTI 1.3 Integration in Your LMS

The python version of Materia uses LTI 1.3 to communicate with Canvas, which is a completely seperate implementation of LTI from prior versions of Materia. As such, you will need to reinstall Materia as a LTI 1.3 integration in your LMS. Fortunately, Canvas intelligently migrates existing resource links (widgets embedded as assignments or module items) from using the LTI 1.1 tool to LTI 1.3, provided the domain is the same.

We provide step-by-step guidance for setting up the LTI 1.3 integration in our [Canvas LTI Setup](canvas-lti-setup.html) page.

## Retire The Old LTI 1.3 Integration

Once your LTI 1.3 integration is configured and working as expected, it's safe to disable and remove the LTI 1.1 version of the tool in Canvas.

## Celebrate!

That's it - you're done! Enjoy using python Materia, and don't forget to submit bug reports as [issues in GitHub](https://github.com/ucfopen/Materia/issues).