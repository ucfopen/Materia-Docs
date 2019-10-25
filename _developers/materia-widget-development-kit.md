---
title: Materia Widget Dev Kit
tagline: Developing Widgets made easier with the MWDK
class: developers
category: widgets
---

Materia Widget Development Kit (MWDK) is a local development environment for quickly building Materia Widgets.

The MWDK automatically watches changes to your widget source files and re-builds assets on the fly using an in-memory webpack dev server. Just save your file and reload the browser to see changes.

## Some MWDK Features include:

* Supports player and creator html files
* Uses the same Javascript as the production Materia Server
* Utility for easily building .wigt packages
* Materia API mocking
* Reusable Webpack rules common to many widget build processes

{% include figure.html
	url="mwdk/mwdk-home.png"
	alt="MWDK home screen showing the Crossword widget."
%}


## Build Widget Players

MWDK loads your widgets demo.json QSET into the player automatically.  It handles all API calls a widget makes to the Engine Core.

{% include figure.html
	url="mwdk/mwdk-player.png"
	alt="MWDK being used to develop the Crossword widget player."
%}


## Build Widget Creators

MWDK loads and displays your creator for easy development and testing. There is a mock question set importer and a mock media importer for testing those features in your creator. It handles all API calls a widget makes to the Creator Core.

{% include figure.html
	url="mwdk/mwdk-creator.png"
	alt="MWDK being used to develop the Crossword widget creator."
%}


## Package WIGT files

To aid in widget packaging and testing, MWDK includes features to build and download a .wigt file.  If you're using Materia Docker to run a local development Materia Server - MWDK has a script to install it to the server for you.

{% include figure.html
	url="mwdk/mwdk-package.png"
	alt="MWDK has a GUI interface to build .wigt files and install into a Docker Materia based local dev server"
%}



## Using the MWDK

The MWDK is an [npm package](https://www.npmjs.com/package/materia-widget-development-kit) that is included as a dev dependency in your widget's package.json.

### Install using npm:

```shell
$ npm install materia-widget-development-kit --save-dev
```

### Configuring Your widget

> @TODO

### Using the MWDK's common Webpack Rules

The MWDK offers several default webpack rules that are common across most widget build processes accessed through the webpack-widget file.

The three main functions used to access these rules are:

#### `getDefaultRules()`

This function returns the typical rules used for loading, compiling, and copying development files for production. If you are developing a widget that requires a rule that isn't current default (e.g. React), you will need to add your own rule to your webpack config.

#### `getDefaultCopyList()`

This function returns the default items that need to be copied verbatim from the dev environment to production. See the [wigt package structure](wigt-package.html) to see what assets and directories are required.

#### `getDefaultEntries()`

This function adds the player and creator js and css files to the entries. However, it is reccommended that you create your own entries variable instead of using this function because the function only support CoffeScript for its JavaScript files.

### Running the MWDK


```shell
$ npm run start
```

```shell
$ yarn start
```

### Install with yarn:

```shell
$ yarn add materia-widget-development-kit --dev
```
