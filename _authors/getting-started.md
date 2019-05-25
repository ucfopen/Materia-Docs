---
title: Author's Guide
menu_title: Getting Started
tagline: Instructor's guide to using Materia in your course
class: instructors
category: first
highlighter: no
---
# Customizing a New Widget
The first step in using Materia in your course is to create a new widget with your own custom content. Each widget has a creation interface which allows you to easily customize it to your needs. Once created, the widget can be distributed and shared with students and other instructors.

## Selecting a Widget to Create
Begin by logging in to Materia using the appropriate credentials, and selecting the *Widget Catalog* link at the top of the page.

{% include figure.html
	no_thumb="true"
	url="click_profile_link.png"
	alt="Select the <strong>Widget Catalog</strong> link to view the widgets available for use."
%}

The catalog page provides a list of available widgets, with filtration options on the left. Mousing over a widget provides an overview of their features and supported data.

{% include figure.html
	url="widget_catalog.png"
	alt="Select a widget to create from the Materia widget catalog."
%}

Selecting a widget brings you to the widget detail screen with screen shots of the widget in action, and links to play a demo or to build your own.

{% include figure.html
	url="widget_detail.png"
	alt="The detail page provides you with an overview of the selected widget."
%}

## Widget Creator Basics

Each widget has a unique creator designed specifically to help you customize the content needed for that type of widget. While the details will change from widget to widget, all creators have several universal features in common. For details on creators for specific widgets, you may find them under the *Creating Widgets* menu at the top of this page.

## The Creator Page
Below is an example of the *Hangman* widget creator.

{% include figure.html
	no_thumb="true"
	url="creator_example.png"
	alt="Screenshot showing the hangman creator."
%}

0. Question importing, Publish, Preview, and Save Draft features. *Note: these buttons are standard across all creators.*
0. Adding questions and content. *This will be different for each individual creator.*
0. Widget title. *This is standard across all creators.*
0. Widget options. *These will be different for each individual creator.*

## Developing Questions for your Widget
In most cases, content for widgets is comprised of a *question* or *hint*, and an *answer* or *series of answers* for a given question. In this example, our Hangman widget has a question that is initially posed to the student and an answer that is considered the correct response. Some widgets, such as *Enigma* or *Choose your Own Adventure*, can have multiple responses to each question.

Note that each creator typically has a text box in which to enter a new question, as well as a text box (or boxes) in which to enter an answer or multiple answers. Each creator also has options to add new questions or to remove existing ones. Some widgets allow media such as images or audio to be used to supplement or replace questions.

## Importing Questions
Individual questions you created for previous widgets may be imported into the creator using this feature. When selected, a pop-up dialog will open listing other questions you've created. Simply select any number of these and click the **Import Selected** button at the bottom.

{% include figure.html
	no_thumb="true"
	url="import_questions.png"
	alt="Screenshot showing the question importer."
%}

## Defining Widget Options
Many widgets contain options in addition to the questions, answers, and media you include. These options are largely widget-specific and are covered in the guides for each individual widget.

## Previewing, Drafts, and Publishing
All creators share the same options once your widget is ready to go.

* **Preview**: This opens a new window and allows you to preview your widget, experiencing it as your students will.
* **Save Draft**: Drafts are incomplete widgets you can return to finish later. Once a draft is saved, it will show up in your list on the *My Widgets* page. Select the widget and click **Edit Widget** to return to the creator and continue editing.
* **Publish**: A published widget is completed and ready to be shared with your students. Like drafts, published widgets appear on the *My Widgets* page.

{% include figure.html
	no_thumb="true"
	url="publish-preview-save-draft-buttons.jpg"
	alt="The Publish, Preview, and Save Draft buttons help you build and edit your widgets."
%}

## Guest Widget Instances

Guest widgets serve 2 main functions:

1. All play data is completely anonymous, even if the player is logged in.
2. Players are not required to log in.

These features are important for several use cases.  If you wish to publicly share a widget instance without requiring users to authenticate, a guest instance allows it.  For instance, the demo widget instances linked from the catalog are all guest widgets.

In Materia, students have the ability to create widgets. This opens up some new assignment opportunities for faculty; however, it does create some problems pertaining to student data and privacy.  This is why Materia forces all widgets created by students into Guest Mode.  This helps avoid possible [FERPA](https://www2.ed.gov/policy/gen/guid/fpco/ferpa/index.html) issues relating to student data and scores.
