---
title: Developing Materia Widgets
tagline: Create your own widgets!
class: developers
category: widgets
---
# Developing Widgets for Materia

It's easiest to imagine Materia as an app store for small educational apps. It's the server that hosts and manages those individual apps (or **widgets** in Materia speak) in one centralized, integrated platform.

Widgets are packaged into a single file and easily installed with a single command. This page describes the widget development process and tools required to create a widget package that can be installed in any Materia distribution.

## What Language are Widgets Written In?

Most widgets are built with HTML, CSS, and Javascript.  However, just about anything viewable in a browser that talks to Javascript will work.  This guide will focus on building Javascript widgets.  If you're using something more exotic, this will still be useful to understand how to wrap your fancy gadgets into a Materia widget.

## Development Environment

The [Materia Widget Development Kit](materia-widget-development-kit.html) provides the best environment to build and test widgets.  It's a combination of an Express.js server, a splash of javascript borrowed from the Materia Server, and a little bit of webpack to glue it all together.  Check out the MWDK page to get set up and running.

## What's a Widget

A widget is a little packaged application that adheres to a few basic requirements.  It has full control over what happens within it's iframe, with a simple API to get data and save scores to the server. The simplest widgets can be simply static web pages.

---

## The Player

A widget player is just an html page.  You can place whatever you like on the page like graphics, Javascript, Flash, Unity, whatever.  To load content and save scores, you'll need to load a javascript file, `materia.enginecore.js`.  The Engine Core abstracts communicating with Materia into a simple API.

Here's an empty player.html file:

```html
<!DOCTYPE html>
<html>
	<head>
		<title>A Basic Widget Player</title>
		<script src="materia.enginecore.js"></script>
	</head>
	<body>
		<script>
			Materia.Engine.start({
				start: (instance, qset, qsetVersion) => {
					// process the qset and start your app
				}
			})
		</script>
	</body>
</html>
```

> See the [Materia Engine Core API](engine-core.html) for details on all of it's magical super powers.

### A Typical Sequence of Events in The Player

1. Call `Materia.Engine.start({...})` to signal that your widget has loaded.
2. The Engine Core will load instance content from the server and pass it to your `start` callback.
3. Your widget processes the instance and qset data and do whatever amazing things your widget does.
4. As the user progresses, send logs, scores, and data to the server.
5. Finish by calling `Materia.Engine.end()`, Materia will show the score screen.

---

## The Creator

A creator, like [the player](#the-player), is just an HTML page with your assets and code.  Just like the player, there is a helper script to talk to the Materia API: `materia.creatorcore.js`.

A creator is totally optional, but if you want to allow users to create customized content for widgets, you'll need one.

Here's an empty creator.html file, it's got a few special properties you'll want to define:

```html
<!DOCTYPE html>
<html>
	<head>
		<title>A Basic Widget Creator</title>
		<script src="materia.creatorcore.js"></script>
	</head>
	<body>
		<script>
			let _title = ''
			let _qset = {}

			Materia.CreatorCore.start({
				initNewWidget: (widget, baseUrl, mediaUrl) => {
					// user is creating a new widget
					// display your custom input interface
				},
				initExistingWidget: (widget, title, qset, qsetVersion, baseUrl, mediaUrl) => {
					// user is editing an existing widget
					_title = title
					_qset = qset
				},
				onSaveClicked: (mode = 'save') => {
					// possible modes: 'publish', 'preview', 'save'
					// user clicked save, convert our data into a qset and save it
					Materia.CreatorCore.save(_title, _qset)
				},
				// onSaveComplete: (instanceName, widget, qset, qsetVersion) => {},
				// onMediaImportComplete: (arrayOfMedia) => {},
				// onQuestionImportComplete: (arrayOfQuestions) => {},
			})
		</script>
	</body>
</html>
```

> See the [Materia Creatore Core API](creator-core.html) for details on creating and updating widgets.

### A Typical Sequence of Events in The Creator

1. Call `Materia.CreatorCore.start({...})` to signal that your creator has loaded.
2. The Creator Core will load content from the server and pass it one of your `initWidget` callbacks.
3. Your creator processes the instance and qset data and lets the user customize the content.
4. The user clicks Save
5. Creator Core calls `onSaveClicked` where you build a qset and call `Materia.CreatorCore.save`


---

## The Score Screen

Materia provides a standard score screen that will be fine for displaying scores and answer feedback in most cases.

However, for more advanced uses a custom score screen can be provided.  It's *just another* html page with some Javascript, this time using `materia.scorecore.js` to talk to the API.

```html
<!DOCTYPE html>
<html>
	<head>
		<title>A Basic Score Screen</title>
		<script src="materia.scorecore.js"></script>
	</head>
	<body>
		<script>
			// hide the default results table
			Materia.ScoreCore.hideResultsTable()
			// start & register a callback
			Materia.ScoreCore.start({
				start: (instance, qset, scoreTable, isPreview, qsetVersion) => {
					// build a custom score display here
				}
			})
		</script>
	</body>
</html>
```

> See the [Materia Score Core API](score-core.html) for score screen API details.

---

## Typical Source Code File Structure

<pre>
├── /src
│   ├── /<a href="wigt-package.html#display-icons">_icons</a>               # REQUIRED - unique icon in multiple sizes
│   ├── /<a href="wigt-package.html#screen-shots">_screen-shots</a>        # REQUIRED - screenshots for widget detail page
│   ├── /_score
│   │   └── /<a href="score-module.html">score_module.php</a> # server side score checking
│   ├── /assets               # js, css, assets for demo.json
│   ├── <a href="widget-developer-guide.html#the-player">player.html</a>           # REQUIRED
│   ├── <a href="widget-developer-guide.html#the-creator">creator.html</a>
│   ├── <a href="widget-developer-guide.html#the-score-screen">scoreScreen.html</a>
│   ├── <a href="question-structures.html#demojson">demo.json</a>             # REQUIRED - A qset used for the demo instance
│   └── <a href="install-yaml.html">install.yaml</a>          # REQUIRED - Installation settings
├── README.md
├── package.json
└── webpack.config.js
</pre>



## Compiling with Webpack

Here is a basic example of a webpack config for a widget. See the [materia widget development kit page](materia-widget-development-kit.html) for an explanation of the widgetWebpack functions called.

```javascript
const path = require('path')
const srcPath = path.join(__dirname, 'src') + path.sep
const outputPath = path.join(__dirname, 'build') + path.sep
const widgetWebpack = require('materia-widget-development-kit/webpack-widget')

const rules = widgetWebpack.getDefaultRules()
const copy = widgetWebpack.getDefaultCopyList()

const entries = {
	'player': [
		path.join(srcPath, 'player.html'),
		path.join(srcPath, 'player.js'),
		path.join(srcPath, 'player.scss'),
	],
	'creator': [
		path.join(srcPath, 'creator.html'),
		path.join(srcPath, 'creator.scss'),
		path.join(srcPath, 'creator.js')
	]
}

const options = {
	entries: entries,
	moduleRules: rules,
	copyList: copy
}

const buildConfig = widgetWebpack.getLegacyWidgetBuildConfig(options)

module.exports = buildConfig
```
