---
title: Score Manager API
menu_title: Widget API - Score Manager
tagline: Use the Score Manager to Save Score Data
class: developers
category: widgets api
---
# Materia Score Manager

The score manager provides methods for sending score data to the server for a widget player.

## Materia.Score.submitInteractionForScoring

Widget interactions are a catch-all category for any (logged) widget activity that isn't categorized as an answered question or final score.

It's up to the [score module](score-module.html) to make sense of the interaction and grade the widget appropriately.

Examples include an individual question modifier (hint used, -50%), an overall score modifier (-20% to final score), or more esoteric cases.

| Argument | Required | Description
|-
| QuestionId | **yes** | questionID the ID of the question associated with this interaction, if applicable. The [score module](score-module.html) can ignore it for cases where it doesn't apply.
| interactionType | **yes** | A string identifying what the interaction is, e.g.: 'hint_used', 'attempt_penalty', etc.
| value | **yes** | The value of the interaction, if applicable.

### Example

```javascript
Materia.Score.submitInteractionForScoring(question.id, 'hint_used', 10)
```

## Materia.Score.submitFinalScoreFromClient

A final score submission from the client. In some situations, a widget may not pass back logs for individual questions/interactions, and only pass back a final score. For example, perhaps the widget scores on the client side and only provides the score.

| Argument | Required | Description
|-
| questionID | **yes** | if the final score is being determined by an individual question, its ID can be used here. Otherwise, just use 0.
| userAnswer | **yes** | if the final score is determined based on a user's answer. Can be an empty string otherwise.
| score | **yes** | the final score to return.

### Example

```javascript
Materia.Score.submitFinalScoreFromClient(0, '', 95)
```

## Materia.Score.submitQuestionForScoring

An answered question submission. This is the most basic log type. Used in most ordinary responses for individual questions.

| Argument | Required | Description
|-
| questionID | **yes** | The ID of the question being answered
| userAnswer | **yes** | The response the user provided. This string is matched against the widget's QSET on the server to determine the correct answer.
| value | no | The value isn't by default used to determine the score of the question, however it can be used to pass an additional value to be used in scoring

### Example

```javascript
Materia.Score.submitQuestionForScoring(question.id, answer.value)
```

## Materia.Score.addGlobalScoreFeedback

Adds a message/feedback to the overall score screen.

| Argument | Required | Description
|- message | **yes** | Message to display on the score screen

### Example

```javascript
Materia.Score.addGlobalScoreFeedback('You lost your companions escaping from the dungeon.')
```

## Materia.Score.addScoreData

Adds an unspecified type of score data for processing by a custom [score module](score-module.html).

| Argument | Required | Description
| data | **yes** | Object to store in the score logs

### Example

```javascript
Materia.Score.addScoreData({action:'restart_level', value:5})
```
