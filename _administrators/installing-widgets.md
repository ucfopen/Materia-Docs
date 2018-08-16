---
title: Installing Widgets
tagline: for your copy of Materia
class: admin
---


# Installing the widget packages #

A basic Materia installation comes with a root `/packages` directory which contains all the `.wigt` packages.  Use the widget install task to install all widgets in this `/packages` directory:

``` shell
$ php oil r widget:install --auto-upgrade
```

This will install any new widgets as well as widgets that have been changed and will skip over widgets that are already installed.

## Installing widget(s) outside of the packages directory ##

If you'd like you can install one or more widgets that you specify by running

``` shell
$ php oil r widget:install myWidget.wigt
```

> For more information on all the options provided by the widget task run `php oil r widget:install --help`

# Installing widgets from source #

Please read the [Developer's Guide]({{ site.baseurl }}/develop/widget-developer-guide.html) for more information on installing widgets from source.
