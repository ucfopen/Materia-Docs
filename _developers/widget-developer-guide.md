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

Most widgets are built with HTML, CSS, and Javascript.  However, just about anything viewable in a browser that talks to Javascript will work.  This guide will focus on building Javascript widgets.  If you're using a something more exotic, this will still be useful to understand how to wrap your fancy gadgets into a Materia widget.

## Development Environment

The [Mateira Widget Development Kit](materia-widget-development-kit.html) provides the best environment to build and test widgets.  It's a combination of an Express.js server, a splach of javascript borrowed from the Materia Server, and a little bit of webpack to glue it all together.  Check out the MDK page to get setup and running.

## The Anatomy of a Widget

A widget is a little packaged application that adheres to a few basic requirements.  It has full control over what happens within it's iframe, with a simple api to get data and save scores to the server. The simplest widgets can be simply static web pages.

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
3. Process the instance and qset data from the server and do whatever amazing things your widget does.
4. As the user progresses, send logs, scores, and data to the server.
5. Finish by calling `Materia.Engine.end()`, Materia will show the score screen.


## The Creator

A creator, like [The Player](#the-player), is just an HTML page with your assets and code.  Just like the player, there is a helper script to talk to the Materia API: `materia.creatorcore.js`.

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
				onSaveClicked: (saveMode = 'save') => {
					// user clicked save, convert our data into a qset and save it
					Materia.CreatorCore.save(_title, _qset)
				},
				// onSaveComplete: (a,b,c,d) => {},
				// onMediaImportCompelte: (media) => {},
				// onQuestionImportComplete: (question) => {},
			})
		</script>
	</body>
</html>
```

> See the [Materia Creatore Core API](creator-core.html) for details on creating and updating widgets.

## The Score Screen

Materia provides a standard score screen that will be fine for displaying scores and answer feedback in most cases.

However, for more advanced uses a custom score screen can be provided.  It's *just another* html page with some javascript, this time using `materia.scorecore.js` to talk to the API.

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

> See the [Materia Score Core API](score-core.html) for score screen api details.


## Typical Source Repository Layout

```shell
├── /src
│   ├── /_icons              # REQUIRED - unique icon in multiple sizes
│   ├── /_screen-shots       # REQUIRED - screen shots for widget detail page
│   ├── /_score              # REQUIRED - server side score checking code
│   ├── /assets              # js, css, you know, assets
│   ├── player.html          # REQUIRED - The Player
│   ├── creator.html         # The Creator
│   ├── scoreScreen.html     # Custom Score Screen
│   ├── demo.json            # REQUIRED - A qset used for the demo instance
│   └── install.yaml         # REQUIRED - Installation settings
├── README.md
├── package.json
└── webpack.config.js
```

> @TODO make sure a section exists for each of the required options above

## Installation Config: install.yaml

Every widget needs an `install.yaml` file which describes the various settings that will be put into your Materia database.

Here is an example install.yaml file from the Crossword widget:

```
---
general:
  name: Crossword
  group: Materia
  height: 573
  width: 715
  in_catalog: Yes
  is_editable: Yes
  is_playable: Yes
  is_qset_encrypted: Yes
  is_answer_encrypted: Yes
  is_storage_enabled: No
  api_version: 2
files:
  creator: creator.html
  player: player.html
  flash_version: 10
score:
  is_scorable: Yes
  score_module: Crossword
meta_data:
  features:
	- Customizable
	- Scorable
	- Mobile Friendly
  supported_data:
	- Question/Answer
  about: 'In Crossword, fill in the blank squares with: (a) words based on the clues provided in the text and/or (b) by the letters overlapping from other words.'
  excerpt: >
	A quiz tool that uses words and clues to
	randomly generate a crossword puzzle.
```

### General Settings

* **name:** The displayed name of the widget
* **group:** The group name for the widget.
* **height:** The height of the widget in pixels. Use 0 if the widget should expand to the full height available.
* **width:** The width of the widget in pixels. Use 0 if the widget should expand to the full width available.
* **in_catalog:** 'Yes' if the widget should be publicly displayed on the widget catalog for creation and use. Generally, widgets not displayed in the catalog are specialized and lack a creator and are only available for creation through command line.
* **is_editable:** Instances can't be saved as drafts if not editable.
* **is_playable:** 'Yes' if widget instances can be played. 'No' to disable playing of instances. This is typically only used when developing a widget to prevent users from seeing an unfinished widget.
* **is_qset_encrypted:** Tells Materia whether to return an encrypted qset, or unchanged.
* **is_answer_encrypted:** Reserved for future use.
* **is_storage_enabled:** 'Yes' if this widget uses the storage API features. 'No' otherwise.
* **api_version:** Corresponds to which version of the widget instance object this widget expects. You should specify version 1 here.

> Group is currently only for your organizational purposes. Later versions of Materia may use this property to help better organize the widget catalog.

### Files Settings

* **creator:** Location of the creator html file. Not required if <strong>is_editable</strong> is set to 'No.'
* **player:** Location of the player html file.

### score

> Widgets that don't record scores still require a score module, though scoring logic may be omitted.

* **is_scorable:** 'Yes' if the widget collects scores. 'No' otherwise.
* **score_module:** Name of the score module class (in score_module.php).
* **score_type:** (Deprecated) Specifies how a widget is graded. Accepted values are:
	* SERVER - means grading will be handled by a Score Module on the server. **Preferred**
	* CLIENT - means your widget will tell Materia what the widget score should be.
	* SERVER-CLIENT - utilizes both methods.

### meta_data

* **features:** A list of features which will be presented in the widget catalog.
	> While your widget can specify any number of features, Materia specifically looks for two defined features. If your widget is scorable you'll want to include <em>Scorable</em> here. If your widget includes a creator you'll want to include <em>Customizable</em>. These features allow users to filter the catalog page to find the widget they're looking for.
* **supported_data:** A list of the types of data which this widget supports. This will be presented in the widget catalog.
	>Similarly, Materia looks for a few specific features here: <em>Question/Answer</em> and/or <em>Multiple Choice</em>.
* **about:** The text displayed on the widget detail page.
* **excerpt:** The text displayed on the widget catalog page.


## Demo Question Set: demo.json

This file provides a title and qSet which will be used when installed to create a widget demo.
## Example

```
---
name: Math Quiz
qset:
  version: 1
  data:
    questions...
```

## Optional Demo Media Assets:

If your demo has media assets, include them in the optional **assets** folder, and reference them in your demo.json file with `<%MEDIA="assets/1.jpg"%>`, `<%MEDIA="assets/2.jpg"%>`, and so on (replacing `1.jpg`, `2.jpg`, etc. with the name of your asset files). The install script will find any `MEDIA` tags, upload the assets, and replace the tags with the resulting asset IDs before creating and installing the demo.

## Optional Score Module: ```src/_score/score-module.php```

You'll need both a score module and unit test file if your widget is scorable.  The `score_module.php` file is a php class which extends `Score_Module`.  Your score module should override the `checkAnswer` method.  Your implementation of this method should return a number of 0-100 representing the score for the given question response.  The `$log` object contains any data saved to the server by your widget (usually question or performance data).

### Basic score module example

```php
<?php
namespace Materia;

class Score_Modules_MyWidget extends Score_Module
{
	public function check_answer($log)
	{
		if (isset($this->questions[$log->item_id]))
		{
			$question = $this->questions[$log->item_id];
			foreach ($question->answers as $answer)
			{
				if ($log->text == $answer['text'])
				{
					return $answer['value'];
					break;
				}
			}
		}

		return 0;
	}
}
```

This example uses `Score_Module's` `questions` property which contains a reference to this widget instances' `qSet`.

> Look at the <code>Score_Module</code> class for all of the properties available to you.

## Display Icons: `/src/_icons`

This folder should contain the icons for your widget. A total of four icons at various pixel sizes are required:
* icon-60.png
* icon-60x2.png
* icon-92.png
* icon-92x2.png
* icon-275.png
* icon-275x2.png
* icon-394.png
* icon-394x2.png

## Overview Screen Shots: `/src/_screen-shots`

This folder should contain screen shots and corresponding thumbnails for your widget.  These will be used in the detail page for your widget.

* 1-thumb.png
* 1.png
* 2-thumb.png
* 2.png
* 3-thumb.png
* 3.png


# Compiling with Webpack

> @TODO

