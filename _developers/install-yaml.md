---
title: Widget Install.yaml
tagline: Widget Instal Configuration
class: developers
category: widgets
highlighter_langs:
  - yaml
---
# Materia Widget Install Configuration

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
