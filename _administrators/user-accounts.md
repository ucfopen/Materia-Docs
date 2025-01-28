---
title: User Accounts
tagline: Create and manage user records in the Materia database.
class: admin
category: maintenance
---

# A Note on User Management

when evaluating Materia, keep in mind that the system does not provide an out-of-the-box solution to handle user authentication at scale. Generally, we expect users to authenticate ahead of the system through an institutional login or through their LMS. The latter is easier to configure, works immediately, and is our generally recommended authentication solution.

> You can continue to create and administer local-to-Materia users (such as service accounts) alongside user records created through these external authentication methods.

## LMS Authentication

Materia is designed to work with Instructure's Canvas LMS. It should be possible to enable LTI integration with additional Learning Management Systems, and we'd happily review code contributions that enable this behavior.

Consult the [Canvas LTI Setup](canvas-lti-setup.html) page to integrate Materia with your instance of Canvas.

## Direct Authentication

While it is possible to enable direct authentication, we do not provide detailed guidance on the authoring of a custom authentication module. Review [FuelPHP's auth package documentation](https://fuelphp.com/docs/packages/auth/intro.html) to get started. Generally speaking, a custom auth package includes or performs the following:

* Installation as a `fuel-package` via composer.
* A `bootstrap.php` file that registers classes in the autoloader and login event listeners (`lti_get_or_create_user` and `request_login`).
* Leverages third-party authentication libraries (for example, `php-saml`) to enable logins via redirect to an external auth endpoint.
* Validates auth status, and receives login user data from payload (for example, `$saml->getAttributes()`).
* Uses FuelPHP/Materia auth driver to create or modify user records based on login user data.

> Note that the next major version of Materia will include significant changes to authentication due to a transition from FuelPHP to Django.

## Service Users

Creation of service users is best performed through an oil task. Note that like all oil commands, it must be performed in the context of the `app` container. Refer to the [docker setup page](setup-configure-docker.html) for instructions for gaining shell access to the `app` container.

### New User Creation

```shell
php oil r admin:new_user <username> <first> <mi> <last> <email> <password>
```

### Granting Roles

Roles can be assigned or revoked through an additional oil command. Valid roles are:

* `basic_author` is intended for faculty and staff and represents normal access.
* `support_user` provides elevated access for support staff. It must be provisioned (and revoked) manually. Support users can provide certain administrative actions for users and instances, and can view student performance data for any instance.
* `super_user` does what it says on the tin. Superusers have implicit access to everything, including the widget admin panel, which allows for the administration, installation, and updates of widget engines. **We recommend creating a dedicated superuser account for these actions, and not granting this role to regular users**.

```shell
php oil r admin:give_user_role <username> <role>
```
