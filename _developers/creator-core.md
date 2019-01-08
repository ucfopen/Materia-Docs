---
title: Creator Core API
menu_title: Widget API - Creator Core
tagline: Use the Creator Core to Customize Widget Instances
class: developers
category: widgets api
---
# Materia Creator Core

The creator core is loaded via `materia.creatorcore.js` in a widget's creator html file.  Use it to load and save content for creating custom widget instance data.

## Materia.CreatorCore.start

Signal to Materia that the widget has loaded and pass it callback methods for various creator functions.  View the example for all supported callback method signatures.

Below is a table of the supported keys for start's argument object

| Object Property | Required | Description
|-
| initNewWidget | **yes** | Callback for when a new instance is being created
| initExistingWidget | **yes** | Callback for when an existing instance is being created
| onSaveClicked | **yes** | Callback after user clicks save, before actually saving
| onSaveComplete | **yes** | Callback after save completed
| onMediaImportComplete | no | Callback after media import has completed
| onQuestionImportComplete | no | Callback after question import has completed
| manualResize | no | set to `false` automaticaly adjust the height of the widget based on [window.getComputedStyle](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle)

### Example

```javascript
Materia.CreatorCore.start({
	initNewWidget: (widget, baseUrl, mediaUrl) => {},
	initExistingWidget: (widget, title, qset, qsetVersion, baseUrl, mediaUrl) => {},
	onSaveClicked: (mode = 'save') => {}, // possible modes: 'publish', 'preview', 'save'
	onSaveComplete: (instanceName, widget, qset, qsetVersion) => {},
	onMediaImportComplete: (arrayOfMedia) => {},
	onQuestionImportComplete: (arrayOfQuestions) => {},
	manualResize: false // set to false to turn on automatic Resizing
})
```

## Materia.CreatorCore.alert

Display an alert over the entire page.

### Example

```javascript
Materia.CreatorCore.alert('Alert Title', 'Alert Message')
```

## Materia.CreatorCore.showMediaImporter

Display Materia's media importer.  The importer will allow the user to upload and choose media files to insert into the widget.  To make use of this method, make sure you define a `onMediaImportComplete` callback with `Materia.CreatorCore.start`.

| Argument | Required | Description
|-
| mediaTypes | **yes** | Array of media types the user is allowed to upload. Supported types: `jpg`, `gif`, `png`, and `mp3`

### Example

```javascript
// import images
Materia.CreatorCore.showMediaImporter(['jpg', 'gif', 'png'])

// import audio
Materia.CreatorCore.showMediaImporter(['mp3'])
```

## Materia.CreatorCore.getMediaUrl

Convert a Materia asset id into a url.

| Arguments | Required | Description
|-
| mediaId | **yes** | Id of the media file to convert

## Materia.CreatorCore.directUploadMedia

Send media data directly to the media uploader.  Use if the creator has it's own file picker or generates media programatically.

| Argument | Required | Description
|-
| mediaData | **yes** | Object with `name`, `mime`, `ext`, `size`, and `src` properties.

### Example

```javascript
let file = event.target.files[0]
let reader = new FileReader()
reader.onload = event => {
	let mediaData = {
		name: file.name,
		mime: 'image/jpg',
		ext: file.name.split('.').pop(),
		size: file.size,
		src: event.target.result
	}

	Materia.CreatorCore.directUploadMedia(mediaData)
}

reader.readAsDataUrl(file)
```

## Materia.CreatorCore.cancelSave

Used to inform the user that a save request is not able to be fufilled.

| Argument | Required | Description
|-
| message | **yes** | String message to show to the user.


### Example

```javascript
Materia.CreatorCore.cancelSave(message)
```

## Materia.CreatorCore.save

Used to inform the user that a save request is not able to be fulfilled.

| Argument | Required | Description
|-
| title | **yes** | String. Name or title of the widget instance.
| qset | **yes** | Object. [Qset data object](question-structures.html).
| qsetVersion | **yes** | Integer. Indicates the format version of the widget engine's qset.

> If your widget changes the structure of it's qsets, you can use the qsetVersion to indicate the change and retain processors for previous versions.


### Example

```javascript
Materia.CreatorCore.save('My new widget', { version: 1, data: {...} }, 1)
```

## Materia.CreatorCore.disableResizeInterval

Disables automatic resizing of the widget iframe. See [Materia.CreatorCore.start](#materiacreatorcorestart)

### Example

```javascript
Materia.CreatorCore.disableResizeInterval()
```

## Materia.CreatorCore.setHeight

Manually set the pixel height of the widget.

### Example

```javascript
Materia.CreatorCore.setHeight(200)
```

## Materia.CreatorCore.escapeScriptTags

Utility function for removing html tags tags from a string

### Example

```javascript
// returns `&lt;this should be fine&gt;`
Materia.CreatorCore.escapeScriptTags('<this should be fine>')
```
