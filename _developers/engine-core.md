---
title: Engine Core API
menu_title: Widget API - Engine Core
tagline: Use the Engine Core to load and save data from the Player
class: developers
category: widgets api
---
# Materia Engine Core

To load content and save scores from a widget's player component, you'll need to load the Materia Engine Core (`materia.enginecore.js`).  The Engine Core abstracts communicating with Materia into a simple API.

## Materia.Engine.start

Signals that your widget is done loading it's assets and passes a callback that will received widget instance data.

| Object Property | Required | Description
|-
| start | **yes** | A function that is called after the instance and qset data has loaded
| manualResize | no | set to `false` automaticaly adjust the height of the widget based on [window.getComputedStyle](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle)

> Note this is the only method that expects an object with properites

### Example

```javascript
Materia.Engine.start({
	start: (instance, qset, version) => {},
	manualResize: false
})
```

## Materia.Engine.addLog

Queues a log to send to the server.  Logs must conform to certain value constraints.

| Arguments | Required | Description
|-
| type | **yes** | Log type
| itemId | no | an item id that pertains to the log
| text | no | @TODO
| value | no | @TODO

## Materia.Engine.alert

Ask Materia to display a stylized Alert message.

| Arguments | Required | Description
|-
| title | |
| message | |
| type | |

## Materia.Engine.getImageAssetUrl

Convert a materia asset id into a url.

| Arguments | Required | Description
|-
| mediaId | **yes** | Id of the media file to convert

## Materia.Engine.end

Mark the widget play as finished.  No more logs will be accepted after end is called.  By default, calling end will jump to the score screen.

| Arguments | Required | Description
|-
| showScoreScreenAfter | no | Set to `false` to prevent Materia from jumping to the score screen. Defaults to `true`.

## Materia.Engine.sendPendingLogs

Request that the engine core immediately sends any pending logs to the server.  Automatically called by end.

Accepts no arguments.

## Materia.Engine.sendStorage

Do not use directly, UseSee Materia.Storage.Manager


## Materia.Engine.disableResizeInterval

| Arguments | Required | Description
|-

## Materia.Engine.setHeight

| Arguments | Required | Description
|-

## Materia.Engine.escapeScriptTags

| Arguments | Required | Description
|-
