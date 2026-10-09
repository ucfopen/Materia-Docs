---
title: Site Administration
tagline: Content management for your instance of Materia
class: admin
category: maintenance
---

# The Site Admin Panel

As Materia has developed, we've begun to include additional features that allows administrators to customize and manage their own instances on a deeper level. These features can be managed through the site admin panel instead of requiring more involved programmatic maintenance or solutions.

> The site admin panel is available to users with the super user flag exclusively. It is not available to support users.

## Image Management

The Image Management interface allows you to upload or replace images that are used throughout the site:

#### Profile Images

In Materia `v11.1.0` we included a series of default profile pictures that can be selected by all Materia users. These profile pictures were hand-illustrated by our talented team at UCF. Newly provisioned user accounts will be randomly assigned one of these profile images. Users can select one of these profile images by visiting the Settings page from their profile. You can remove any of the existing profile images or add new ones by selecting **Profile Image** in the image upload drop-down.

#### Community Library Featured Banner

The featured section of the Community Library includes a default banner image, but you can choose to upload a new, more thematic image instead by selecting **Library Banner** in the image upload drop-down. We recommend lossless images (such as SVGs) sized at 640x480px.

#### Catalog Banner (NYI)

As of this time the catalog banner is not yet implemented. A future version of Materia will include an updated Catalog UI that will include the ability to upload custom banners to showcase new widget engines.

## Message Management

Message management lets you administrate strings and messages used in several places across your instance. These include:

#### System Notifications

System notifications are non-critical messages that nonetheless are potentially relevant to all users of your Materia instance. This can be used to indicate things like upcoming downtime or maintenance. When a system notification is active, it will be displayed as a banner at the top of the application and as a low-profile toast banner in embedded widget views.

In addition to the notification message, you can schedule messaging in advance by providing **Start At** and **End At** values. The notification will only be displayed during the period specified.

#### System Alerts

System alerts represent critical, time-sensitive messages that are relevant to all users of your Materia instance. This can be used to indicate service outages, system issues, or other events that may impact system operation. System alerts can be activate at the same time as system notifications, and are displayed in the same contexts: in a banner across the top of the application and as a low-profile toast banner in embedded widgets.

Like system notification messages, system alerts can be scheduled to start or end at predetermined times.

#### Catalog Text, Catalog Header (NYI)

These messages will influence the featured section of the widget catalog. They are not currently implemented.

#### Library Text, Library Header

Providing message values for Library Text and Library Header will override the default strings used in the featured section of the Community Library. In conjunction with library banners, they allow you to customize the featured section to highlight Community Library contributions as you see fit.