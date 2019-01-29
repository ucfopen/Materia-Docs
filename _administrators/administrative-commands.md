---
title: Administrative Commands
tagline: Making use of Fuel tasks to automate Materia administration
class: admin
category: administrative
---

# Materia's CLI Management Commands

Materia CLI commands are executed using FuelPhp's **[Oil Utility](https://fuelphp.com/docs/packages/oil/intro.html)**. Oil is a script that resides in the **root directory of Materia**, so all oil commands should be run from there.


## Executing a Materia Task

```shell
$ php oil refine widget:show_engines
```

To break that down:

* `php` is the PHP executable
* `oil` is a FuelPHP [command line utility](https://fuelphp.com/docs/packages/oil/intro.html)
* `refine` is an Oil [utility for running tasks](https://fuelphp.com/docs/packages/oil/refine.html)
* `widget` is the [Widget Task Class](https://github.com/ucfopen/Materia/blob/master/fuel/app/tasks/widget.php) to use
* `show_engines` is the method task from [Admin Task Class](https://github.com/ucfopen/Materia/blob/master/fuel/app/tasks/admin.php)


> You'll likely need to juggle which **FuelPHP environment** your commands run in as it dictates which configuration settings are loaded. Typically you'll just set them inline, for example: `$ FUEL_ENV=production php oil refine admin:show_engines`, but they can be set in a more persistent way.

## Listing Task Class Commands

A list of each task class can be seen by that task with no method argument.

```shell
$ php oil refine admin

available commands:
setup_migrations
destroy_everything
destroy_widgets
destroy_database
populate
populate_semesters
populate_roles
give_user_role
new_user
clear_cache
import_sql
anonymize_users
change_db_prefix
```

## Catalog of Task Commands

| Task | Description
|--
| `install` | The initial Materia server setup wizard.
| `admin:anonymize_users` | This will destructively randomize the username, first name, middle initial, last name, and e-mail address of all users in the database unless specified.<br/> **Args:** `admin:anonymize_users [<EXCEPT_USER_NAME>...]`
| `admin:clear_cache` | Completely clears all cached data (primarily the contents of fuel/app/cache).
| `admin:destroy_database` | This will completely remove all database contents.
| `admin:destroy_everything` | This is a shortcut function, which will run both destroy_widgets and destroy_database.
| `admin:destroy_widgets` | This will uninstall all installed widgets.
| `admin:give_user_role` | This will give the specified role to the specified user. To view role names and permissions, see [here](../develop/platform-developer-guide.html#roles).<br/>  **Args:** `admin:give_user_role <USER_NAME> <ROLE_NAME>`
| `admin:new_user` | Creates a new user from given information.<br/> **Args:** `admin:new_user <USER_NAME> <FIRST_NAME> <MIDDLE_INITIAL> <LAST_NAME> <EMAIL> <PASS>`
| `admin:populate_roles` | This will populate the database with the 'basic author' and 'super user' roles if they have not been added already.
| `admin:populate_semesters` | This is a shortcut function, which will run the semester task's populate function for the years 2001 to 2037.
| `admin:populate` | After all migrations are run, this will fill databases with the information necessary for Materia by running populate_semesters and populate_roles automatically.
| `admin:setup_migrations` | This function will run all of the migrations for Materia, all of the installed modules, and all of the included packages.
| `semester:populate` | This will populate the database with semester information between two given years, including the start and end dates for Spring, Summer, and Fall semesters in each inclusive year.<br/> **Args:** `semester:populate <start year> <end year>`
| `widget:copy_instance` | This will prompt you to log in as a particular user, then specify a widget ID to copy and a name for the new widget. It will attempt to copy the given widget with that user's permissions.
| `widget:create_instance` | `php oil refine widget:create_instance -i`
| `widget:delete_instance` | This will remove all permissions for the specified widget then remove it from the database if possible. Single required argument: widget instance ID.<br/> **Args:** `widget:delete_instance <INSTANCE_ID>`
| `widget:download_package` | Download a .wigt package to the server.<br/> **Args:** `widget:download_package <WIGT_FILE_URL>`
| `widget:export_qset` | Export an existing qset in yaml format.<br/> **Args:** `widget:export_qset <INSTANCE_ID>`
| `widget:install` | Install a .wigt package from a local path. Learn more about [installing widgets](installing-widgets.html). <br/> **Args:** `widget:install [options] [<WIGT_FILE_PATH>...]` (see [options](#options-details-for-widgetinstall-task))
| `widget:show_engines` | List all the widget engines that are installed
| `widget:show_instance` | This will output all information about a widget instance to the screen.<br/> **Args:** `widget:show_instance <INSTANCE_ID>`
| `widget:show_qset` | This will export the specified widget instance's question set to the Materia installs root directory as a yaml file.<br/> **Args:** `widget:show_qset <INSTANCE_ID>`

### Options details for `widget:install` Task
* `--replace-id=<WIDGET_ID>`: Replace by `id`. Overrides default behavior, which uses the widget's `clean_name`.
* `--skip-upgrade`: Does not upgrade a widget if one by the same name already exists.

### Initial Materia Install Task

The `php oil refine install` task is meant to be used on a newly installed Materia server. It will go through the process of asking for various configuration settings and making sure all the files and directories are set up as needed.



