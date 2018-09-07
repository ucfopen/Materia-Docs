---
title: Score Core API
menu_title: Widget API - Score Core
tagline: Use the Score Core to Customize Widget Score Screens
class: developers
category: widgets api
---
# Materia Score Core

The Score Core is used by custom score screens to talk to Materia's API.  Load `materia.scorecore.js` in a widget's score screen html file to gain access to its features.

## Materia.ScoreCore.start

Signals that your score screen is done loading it's assets and passes a keyed object with callbacks to receive data from the server.

### Example

```javascript
Materia.ScoreCore.start({
	start: (instance, qset, scoreTable, isPreview, qsetVersion) => {},
	update: (qset, scoreTable) => {},
	handleScoreDistribution: (distribution) => {}
})
```

## Materia.ScoreCore.hideResultsTable

Tells Materia to not display the default results table.  By default is is shown.  Call this method as soon as possible to reduce UI flashing.

In the figure below, the **results table** is the bottom table of questions, responses and answers.

{% include figure.html
	url="score_screen.png"
	alt="Score results summary for 'TV Show Trivia'."
%}

### Example

```javascript
Materia.ScoreCore.hideResultsTable()
```

## Materia.ScoreCore.hideScoresOverview

Tells Materia to not display the top score overview section (the final score section above the results table).

### Example

```javascript
Materia.ScoreCore.hideScoresOverview()
```

## Materia.ScoreCore.requestScoreDistribution

Gets an anonymous and unsorted array containing all completed scores for a widget for the current semester.

### Example

```javascript
Materia.ScoreCore.requestScoreDistribution()
```

## Materia.ScoreCore.setHeight

Adjusts the height of the score screen in pixels.

### Example

```javascript
Materia.ScoreCore.setHeight(350)
```
