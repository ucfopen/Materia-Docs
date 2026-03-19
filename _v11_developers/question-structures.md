---
title: Question Structures
tagline: Standardized question structures for use in Materia
class: developers
category: widgets
---
# About Materia's Question Sets

Materia stores all customized widget data in Question Sets (colloquially called "qsets"). A Question Set is an arbitrary JSON data structure that widget developers use to organize any data needed to store, display and customize widget content. We intentionally removed most restrictions on this data structure to allow widgets freedom in design and implementation.

The qset, at minimum, contains the following:

```json
{
	version: 1,
	data: { ... }
}
```

The `version` property allows you to version qsets. If you later modify your widget engine you could then support a newer type of qset structure. The `data` property is simply an arbitrary object which you can define with the information you need.

## Demo.json

Each [.wigt file](wigt-package.html) must be packaged with a demo.json containing a sample qset.  This question set is used for the demo that users play to demo the widget once it's installed into Materia.

> Note that media assets can be referenced in demo.json qsets. Just place an image in the src/assets folder and reference it in your demo.json using `<%MEDIA="assets/1.jpg"%>`. The installer will handle uploading the media and replace the tags with the resulting asset IDs.

## Standard Qset Structures

While qset data structures are largely free-form, Materia expects scorable widgets to follow certain conventions in order to identify individual atomic "questions" within a qset. These questions make it easier to score student responses in a widget's score module.

### Question Template

```javascript
{
	// tells Materia this is a question object
	materiaType: 'question',

	// ID assigned by server (never send a value unless you are re-using a question)
	// The creator should pass `null` for new questions
	id: null,

	// type of question. builtin options: [QA, MC], otherwise we recommend an engine-specific value, like "adventure".
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

	// Optional. Question scoped properties, like its location on a map
	options:{
		x: 12,
		y: 234
	}

}
```

When Materia recursively traverses a qset, the following properties are **required** in order for an object to be identified as a question:

1. The `materiaType` property,
2. An `id` property,
3. The `type` property,
4. A `questions` array,
5. An `answers` array, which may be empty.


### Multiple Choice Example

A full qset containing one Multiple Choice question.  This question has two possible answers, one worth 0 percent (incorrect), and the other worth 100 percent (correct).

```javascript
{
	version: 1,
	data:
	{
		// this property's name is up to you,
		// but we recommend `items`
		items: [
			{
				/* question 1 */
				materiaType: 'question',
				id: null,
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

> You can define additional data in your qset and still conform to the standard structure as long as you provide the fields as shown in the example above.  For example, it's common to add an 'options' object either in the data object or in question or answer objects.


## Media Asset Structure

Media assets in a qset should always include the `id` and `materiaType: 'asset'` fields.

```javascript
{
	id: 'cd83Ss', // Media asset IDs are sent by the server at the conclusion of the media selection process
	materiaType: 'asset',
	alt: 'Rocket Duck' // optional parameters can be included. We highly recommend an alt text image description
}
```

### Assets in a Question

Assets within the scope of the entire question (i.e. a song that plays during the question). `options->asset` is the standard location of media assets at level of a qset appropriate for the asset: top-level, in a question, or even within individual answers.

```javascript
{
	materiaType: 'question',
	id: 0,
	type: 'QA',
	questions: [ {text: "Who composed this song?"} ],
	answers: [ {text: "Daft Punk", value: 100} ],
	options: {
		// Asset saved in currentQuestion.options.asset
		asset: {
			materiaType: 'asset',
			id: 'R28ld3',
			type: 'image'
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
			// Asset saved in qset.backgrounds[0]
			{ materiaType: 'asset', id: 'cdd3fs' },
			{ materiaType: 'asset', id: '0pdejF' }
		]
	}
}
```

> Assets can be placed just about anywhere arbitrarily, but we advise you keep them linked with the data that makes the most sense.  If the image is part of the answer, place it in the options of each individual answer.  If the asset is not tied to a question at all, save it outside the scope of that question.
