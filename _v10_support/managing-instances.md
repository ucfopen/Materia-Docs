---
title: Managing Instances
tagline: Instance administration within Materia
class: support
category: management
---

# Widget Instance Management

The instance management interface allows support users to manage and administrate any widget instance created in your institution's copy of Materia. Search for an instance using its name (`"My Labeling Widget"`) or the instance ID, the 5-character alphanumeric string that is unique to each instance (`"w5X3A"`).

{% include figure.html
	url="support_user_admin_3.png"
	alt="A screenshot of the instance administration interface."
%}

As a support user, you can perform several administrative actions on any given instance, including:

- Modify the instance content.
- Add or revoke user access, or modify the access of existing users.
- Modfiy instance settings, including attempt limits and availability dates.
- Delete the instance (or un-delete it).

Several properties of an instance require extra consideration:

- **Student Made** is a special flag applied to instances authored by students, or by users who were students at the time of authoring. Student-made widgets are
forced into Guest Mode.
- **Guest Access** toggles Guest Mode. This option disables authentication requirements and anonymizes response data. Note that score data cannot be de-anonymized! Because the authentication requirement is lifted, Materia does not know who interacts with guest instances.

> Be extra careful when disabling Guest Access for widgets that have student owners or co-owners. Doing so will revoke their access.

- **Student Access** is set to `Yes` if a student is among the owners or co-owners of a widget instance.
- **Embedded Only** is a special setting available to an instance once it has been embedded in an LTI context. This option prevents an instance from being played in a non-LTI context.
- **Embedded** is set to `Yes` if an instance has been embedded in an LTI context. Note that this value may not update until after the instance has been played at least once in this context.

## A Note on Attempt Limits

Materia applies attempt limits on a _per-context_ basis. This means that if an instance is embedded in Course A and Course B, the attempt limit applied when played within Course A will not affect the available attempts when playing in Course B. Therefore, it is potentially possible for a student to have more completed plays than the attempt limit if those plays were initialized in different contexts.

## Extra Attempts

The Extra Attempts dialog is extremely situational and should be used with care. This option allows a support user to grant additional attempts to a specific user beyond the attempt limit for a specific context (in this case, a course ID).
