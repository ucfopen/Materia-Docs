---
title: Managing Users
tagline: User administration within Materia
class: support
category: management
---

# User Management

The user administration interface allows support users to review certain attributes of a Materia user, the instances they own or have access to, as well as their instance play history.

## Looking Up Users

Users can be looked up using their name or email address. It's extremely important to remember that user administration is limited to users within Materia itself. When an instructor or student authenticates with Materia, either through the LMS or via direct login, Materia will verify whether a row in the user table exists with their information. If a row does not exist, Materia will try and create one.

> In some rare circumstances, users logging in to Materia for the first time may encounter an HTTP 500 error.  This is typically due to a collision in email addresses. It may be worth investigating whether the email associated with the user is already in use by another user in Materia.

If a user is part of your institution but does not appear in search results, it is likely they have not interacted with Materia previously. They must authenticate with Materia at least to be eligible for certain actions, like being given access to a widget instance.

## User Admin Reference

{% include figure.html
	url="support_user_admin_1.png"
	alt="A screenshot of the user administration interface."
%}

Most of the data available for a user lookup is read-only, and is used for reference purposes. Some notable properties include:

- **ID:** this integer value is the user's interal user ID. It is the primary key of the user table and is primarily used for database queries and internal logic.
- **Username:** this is the interal-to-Materia username. The value is configured by your institution. Unless users are authenticating directly with the Materia login page, this value is not visible nor important to your users.
- **Email:** Self explanatory. You *can* modify this value, but be careful to ensure the new email is not already in use.
- **Roles:** A user's role determines certain limitations within Materia. Notably, the **instructor** role has full access to all standard Materia features. The **student** role is more limited: student-made instances are forced into Guest Mode, and a student cannot be added as a collaborator to a non-student widget instance
- The **User Settings** section includes the configurable settings available to users on their profile page.

## User Instances

This section is populated with every instance the user has access to, either as an **owner** or a **collaborator**. Provided with each instance is a list of its properties, as well as the option to manage it directly. This is analogous to looking up the instance in the instance admin page.

## Play History

This section details every widget play session initialized by the user:

{% include figure.html
	url="support_user_admin_2.png"
	alt="The user admin play history interface."
%}

There are a few considerations to keep in mind when reviewing user play history:

- The **Time elapsed** value is not to be relied upon for incomplete plays. This is because while some widget engines will transmit logs to the server after every user interaction, many will instead transmit all log data upon submission. The server can extrapolate a play duration for incomplete plays based on the submission time of logs, but if no further logs were transmitted after the play was initialized, the duration will be noted as `1s`, which may or may not be the actual duration the student spent on the page before abandoning the play.

- The **Context** value will be `Web` or `LTI` depending on where the instance was played. If embedded as an assignment in your LMS, the widget will likely be played in an `LTI` context. LTI-enabled plays typically transmit score data back to the LMS, however they will *not* transmit score data if embedded as a module item instead of an assignment. The user admin panel does not provide information as to whether the score passback actually occurred.

> You can use the Context property to help determine whether a grade passback should have occurred (and didn't), or if a student mistakenly played an instance outside of the LTI context.

As a support user, you have the ability to review the score screen associated with a specific play session. Clicking the score percentage for a play will bring up the score screen associated with that play, allowing you to review how the student responded to individual questions.

## Troubleshooting Common Issues

Below are a few examples of common issues related to user management we've seen when supporting Materia:

### Student claims they completed a widget but the gradebook in their LMS did not update

Typically, this is due to one of several reasons:

- The student did not actually complete the widget. You can verify this by reviewing the student's instance play history.
- The student completed the widget, but not in an LTI context. This most often occurs when the assignment loads the external tool (in this case, the Materia widget) in a new tab, and the student mistakenly refreshes the page. Doing so may nullify the LTI session. You can verify whether this is the case by reviewing the **Context** property of the play in question.
- The student completed the widget, but the LTI association timed out. This occurs when a student loads the widget, then leaves the browser tab idle for a period of time (we've seen cases widgets being left idle for several hours.) Much like a login session, the LTI association may time out after a period of inactivity. In this case, the Context property may still show `LTI`, but the Time Elapsed property may be considerable.

