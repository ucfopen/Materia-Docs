---
title: Question Structures
tagline: Standardized question structures for use in Materia
class: developers
category: widgets
---
# About Materia's Question Sets

Materia stores all customized widget data in Question Sets (qSets).  A Question Set is an arbitrary data structure that widget developers use to organize any data needed to store, display and customize widget content.  We intentionally removed all restrictions on this data structure to allow widgets freedom in design and implementation.

The qSet, at minimum, contains the following:

```json
{
	version: 1,
	data: { ... }
}
```

The `version` property allows you to version qSets. If you later modify your widget engine you could then support a newer type of qSet structure. The `data` property is simply an arbitrary object which you can define with the information you need.

## Demo.json

Each [.wigt file](wigt-package.html) must be packaged with a demo.json containing a sample qSet.  This question set is used for the demo that users play to demo the widget once it's installed into Materia.

> Note that media assets can be referenced in demo.json qsets. Just place an image in the src/assets folder and reference it in your demo.json using `<%MEDIA="assets/1.jpg"%>`. The installer will handle uploading the media and replace the tags with the resulting asset IDs.

## Standard qSet Structures

The qSet data property doesn't enforce a schema but Materia defines a standard structure that defines Multiple Choice and Single Answer questions. Conforming to this standard structure allows Materia to add questions to the question bank. Users can then use the 'import question' functionality to re-use questions created with your widget creator. If possible it is recommended to conform to this standard structure.

### Question Template

```javascript
{
	// tells Materia this is a question
	materiaType: 'question',

	// id assigned by server (never send a value unless you are re-using a question)
	id: 0,

	// type of question. builtin options: [QA, MC], custom allowed
	type: 'QA',

	// question data is an array of objects
	// usually we only have one, each must have a *text* property
	questions: [
		{
			text: "2 + 2 = ?",
		}
	],

	// answer data is an array of objects
	// each answer must have *text* and *value* properties
	answers:[
		{
			text: "2",
			value: 0

			// Optional. Answer scoped properties, like feedback
			options:{
				feedback: "Try again!"
			}
		}
	],

	// Optional. Question scoped properties, like it's location on a map
	options:{
		x: 12,
		y: 234
	}

}
```

### Multiple Choice Example

A full qSet containing one Multiple Choice question.  This question has two possible answers, one worth 0 percent (incorrect), and the other worth 100 percent (correct).

```javascript
{
	version: 1,
	data:
	{
		// this property's name is up to you
		myQuestions: [
			{
				/* question 1 */
				materiaType: 'question',
				id: 0,
				type: 'MC',
				questions:
				[
					{text: "2 + 2 = ?"}
				],
				answers:
				[
					{text: "2", value: 0},
					{text: "4", value: 100},
				]
			},
			{ /* question 2 */ },
			{ /* question 3 */ }
		]

	}
}
```

### Question/Answer Example

A Question/Answer question.  This question has one correct answer.

```javascript
{
	materiaType: 'question',
	id: 0,
	type: 'QA',
	questions:
	[
		{text: "2 + 2 = ?"}
	],
	answers:
	[
		{text: "4", value: 100}
	]
}
```

> You can define additional data in your qSet and still conform to the standard structure as long as you provide the fields as shown in the example above.  For example, it's common to add an 'options' object either in the data object or in question or answer objects.


## Media Asset Structure

```javascript
{
	materiaType: 'asset', // tells Materia this is an asset
	id: 'cd83Ss', // id assigned to asset by the server (never send empty)

	// Optional. Asset scoped properties, like a title
	// Applies to **this** asset in **this** qset (does not transfer to other widgets)
	options: {
		title : 'Rocket Duck'
	}
}
```

### Assets in a Question

Assets within the scope of the entire question (i.e. a song that plays during the question).

```javascript
{
	materiaType: 'question',
	id: 0,
	type: 'QA',
	questions: [ {text: "Who composed this song?"} ],
	answers: [ {text: "Daft Punk", value: 100} ],
	options: {
		// Asset saved in currentQuestion.options.audio
		audio:{
			materiaType: 'asset',
			id: 'R28ld3'
		}
	}
}
```

Assets within the scope of the question's answers (i.e. multiple choice where you choose a matching image).

```javascript
{
	materiaType: 'question',
	id: 0,
	type: 'MC',
	questions: [ {text: "Which of the images is a duck?"} ],
	answers:
	[
		{
			text: "",
			value: 100,
			// Asset saved in currentQuestion.answers[0].options.asset
			options: { asset:{ materiaType: 'asset', id: 'cd83Ss' } }
		},
		{
			text: "",
			value: 0,
			options: { asset:{ materiaType: 'asset', id: 'xd3rvR' } }
		}

	]
}
```

Keep an array of assets that aren't associated with the questions at all (i.e. theme backgrounds).

```javascript
{
	version: 1,
	data:
	{
		question:
		{
			materiaType: 'question',
			id: 0,
			type: 'QA',
			questions: [ {text: "2 + 2 = ?"} ],
			answers: [ {text: "4", value: 100} ]
		},
		backgrounds: [
			// Asset saved in qSet.backgrounds[0]
			{ materiaType: 'asset', id: 'cdd3fs' },
			{ materiaType: 'asset', id: '0pdejF' }
		]
	}
}
```

> Assets can be placed just about anywhere arbitrarily, but we advise you keep them linked with the data that makes the most sense.  If the image is part of the answer, place it in the options of each individual answer.  If the asset is not tied to a question at all, save it outside the scope of that question.
