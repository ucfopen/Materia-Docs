---
title: Integration PostMessage Events
tagline: Listening to Materia in the Browser
class: developers
category: platform
---
# Materia Javascript Events

Materia widgets broadcasts events to the page they are embedded in.  These can help your parent application know more about what is happing inside the Materia UI. These events are broadcast using standard [postMessage](https://developer.mozilla.org/en-US/docs/DOM/window.postMessage) events.

## Score Recorded Event `materiaScoreRecorded`

Fired when a widget instance play is completed and the student's score is being shown.

### Properties

| Proptery | Description
|-
| type | The value will be `materiaScoreRecorded`
| widget | a widget instance object, view the Widget_Instance PHP class for properties
| score | A 0-100 integer representing the received score


### Example Javascript

```javascript
// post message event handler
function onPostMessage(event)
{
	// event.data is a JSON string
	let data = JSON.parse(event.data);

	if(data.type == "materiaScoreRecorded")
	{
		alert('For ' + data.widget.title + ' you scored ' + data.score + '%');
	}
}

// assuming widgetIframe is the DOM element of the materia iframe
widgetIframe.addEventListener('message', onPostMessage, false);

```

## Widget Selection Event

Fired when launching an LTI assignment selection and the user selects a widget to "embed".

### Properties

The object that is returned is a widget **instance** containing all the instance and engine data.

| Proptery | Type | Description
|-
| attempts | String Integer | Number of allowed attempts. "-1" is unrestricted.
| clean_name | String | a url friendly name
| close_at | Unix Timestamp | Instance closing date
| created_at | Unix Timestamp | Instance creation date
| embed_url | String | Embed playable instance url
| is_student_made | Boolan | Is the instance made by a student
| student_access | Boolean | Indicates if any student users have access to view scores or change instance settings
| guest_access | Boolean | Is the instance set up for guest access
| height | Integer | Height of the widget in pixels. 0 indicates it scales to fill the container
| id | String | Widget Instance ID
| is_draft | Boolean | is the widget instance a draft (not published)
| name | String | The title of this widget instance
| open_at | Unix Timestamp | When the instance opens, "-1" indicates it is always open
| play_url | String | Non-embeded playable instance url
| preview_url | String | Preview url for instance owners
| user_id | Number | Materia User Id of the user that created the instance
| widget |  Object | Object describing the widget engine
| width | Integer | Width in pixels. 0 indicates it scales to fill the container
| qset |  Object | Custom content (qset) for this instance
| img | String | Url to the widget engine icon
| edit_url | String | Url to edit this widget


### Example Javascript

```javascript
// post message event handler
function onPostMessage(event)
{
	// event.data is a JSON string
	let widgetInstance = JSON.parse(event.data);

	// The widget has been selected - now we can respond to it.
	alert("You selected " + widgetInstance.name);
}

// assuming pickerIframe is the DOM element of the materia assignment iframe
pickerIframe.addEventListener('message', onPostMessage, false);
```
