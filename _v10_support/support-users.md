---
title: Support Users
tagline: Administrating Users and Instances
class: support
category: first
---

# The Support User Role

The `support_user` role, like `super_user`, represents users with elevated permissions to administrate certain aspects of Materia from within the platform itself. Once the role is granted, support users gain access to a special Support page that includes tools for user and instance administration. Unlike super users, support users cannot administrate or modify widget engines themselves; only instances created by users.

> It should go without saying that with great power, comes great responsibility. Use your support user powers with care.

## Granting the Support User Role

While the `basic_author` role can be programmatically applied and revoked from users, most commonly when interacting with an LMS, the `support_user` role can only be applied or removed manually. Note that programmatic role application and removal will **not** revoke the `super_user` role.

## User Administration

Use the user admin panel to look up and manage users in Materia. Note that user management only encompasses accounts within Materia itself; even if you access Materia through your LMS, you cannot manage any aspect of the user related to the LMS.

Visit the User administration page for more information about managing users.

## Instance Administration

As a support user, you have the means to look up any widget instance created within your institution's copy of Materia. Note that while the term **widget** is used interchangeably between widget _engines_ and _instances_, **instances** are the customized copies of individual widget engines that are authored by instructors: for example, a Crossword widget instance about the Space Race, or a Labeling widget instance identifying the parts of a cell.

Visit the Instance Administration page for more information about managing widget instances.