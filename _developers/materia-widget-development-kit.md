---
title: Materia Widget Dev Kit
tagline: Developing Widgets made easier withe the MDK
class: developers
category: widgets
---

Materia Widget Development Kit (MDK) is a local development environment for quickly building Materia Widgets.

The MDK automatically watches changes to your widget source files and re-builds assets on the fly using an in-memory webpack dev server. Just save your file and reload the browser to see changes.

## Some MDK Features include:

* Supports player and creator html files
* Uses the same javascript as the production Materia Server
* Utility for easy wigt package building
* Materia API mocking
* Reusable Webpack rules common to many widget build processes

{% include figure.html
	url="mdk/mdk-home.png"
	alt="MDK home screen showing the Crossword widget"
%}


## Build Widget Players

MDK loads your widgets demo.json QSET into the player automatically.  It handles all API calls a widget makes to the Engine Core.

{% include figure.html
	url="mdk/mdk-player.png"
	alt="MDK being used to develop the Crossword widget player"
%}


## Build Widget Creators

MDK loads and displays your creator for easy development and testing. There is a mock question set importer and a mock media importer for testing those features in your creator. It handles all API calls a widget makes to the Creator Core.

{% include figure.html
	url="mdk/mdk-creator.png"
	alt="MDK being used to develop the Crossword widget creator"
%}


## Package WIGT files

To aid in widget packaging and testing, MDK includes features to build and download a .wigt file.  If you're using Materia Docker to run a local development Materia Server - MDK has a script to install it to the server for you.

{% include figure.html
	url="mdk/mdk-package.png"
	alt="MDK has a GUI interface to build .wigt files and install into a Docker Materia based local dev server"
%}



## Using the MDK

The MDK is a [npm package](https://www.npmjs.com/package/materia-widget-development-kit) that is included as a dev dependency in your widget's package.json.

### Install using npm:

```shell
$ npm install materia-widget-development-kit --save-dev
```

### Configuring Your widget

> @TODO

### Using the MDK's common Webpack Rules

> @TODO

### Running the MDK


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
