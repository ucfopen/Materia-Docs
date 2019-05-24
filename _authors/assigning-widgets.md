---
title: Assigning Widgets
tagline: Details about the flexible assignments in Materia
class: instructors
category: platform
highlighter: no
---
# Distributing a Widget to Students

Materia provides several different methods for widget distribution. The first two are available on the *My Widgets* page, once a widget has been selected from the list. The third option provides the ability to link to a Learning Tools Interoperability (LTI) integration (a standard way of integrating a specific application into a learning management system).

{% include figure.html
	no_thumb="true"
	url="widget_sharing_example_1.png"
	alt="The widget sharing dialog, providing options to share via URL or embed code."
%}

## Share Link
The first - and simplest - option for distributing a widget is to copy the URL provided in the widget sharing section of the page as seen above. Provide this URL to your students and once clicked they will be directed to Materia where they can log in and complete the widget.

> Note that the URL for your local institution's copy of Materia will differ from this example.


## Embed Code
The second option is to use the embed code by selecting the *use the embed code* link underneath the widget's URL. This provides you with a block of HTML code that you may copy and paste into an HTML or web page document. This will offer the widget directly in the page containing the embed code, saving students the step of following a link. Students can log in and complete the embedded widget as they would normally.

By default, embedded widgets will attempt to initiate a play automatically - you can optionally disable this feature by unchecking the 'Autoplay' option or changing `?autoplay=true` to `?autoplay=false` in your embed code. This is a useful feature if you are embedding multiple widgets on the same page, as it will require students to explicitly start playing each one.

## LTI Integration (External Tools in Canvas)
Send scores to your gradebook and support single-sign-on for students. The best option option for sharing widgets is available for instructors using a Learning Management System that supports LTI (most do). Integrating your widgets into Instructure's Canvas is covered in the [Embedding in Canvas](embedding-in-canvas.html) section.

> LTI integrations will require some technical setup from your Materia and LMS administrators.
