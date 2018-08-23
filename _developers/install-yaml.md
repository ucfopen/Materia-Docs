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

```yaml
---
general:
  # REQUIRED. String. Name of this widget (spaces and dashes ok).
  name: Crossword

  # REQUIRED. String. Not used.
  group: Materia

  # REQUIRED. Int. Pixel height of the iframe. Use `0` for 100%
  height: 573

  # REQUIRED. Int. Pixel width of the iframe. Use `0` for 100%
  width: 715

  # REQUIRED. Boolean. Widget is shown in the highlight catalog.
  in_catalog: Yes

  # REQUIRED. Boolean. `No` hides the widget from all catalog views.
  is_playable: Yes

  # REQUIRED. Boolean. Enables edit instance and save as draft buttons.
  is_editable: Yes

  # REQUIRED. Boolean. Does this widget store tabular data using the Storag Manager?
  is_storage_enabled: No

  # REQUIRED. Int. Version of the Materia API this widget uses. Use `2`.
  api_version: 2

  # REQUIRED. Boolean. Not used
  is_qset_encrypted: Yes

  # REQUIRED. Boolean. Not used
  is_answer_encrypted: Yes

files:
  # REQUIRED. String. Name of the the player html file
  player: player.html

  # OPTIONAL. String. Name of the creator html file.
  creator: creator.html

  # REQUIRED. Int. Flash player version required for the player and creator
  flash_version: 0

score:
  # REQUIRED. Boolean. Does this widget score?
  is_scorable: Yes

  # OPTIONAL. String. Name of the Class in the score module
  score_module: Crossword

  # OPTIONAL - name of the custom score screen html file. Omit to use default score screen
  score_screen: ScoreScreen.html

meta_data:
  # REQUIRED. List of Strings. What feature tags should be displayed on the widget detail page
  features:
    - Customizable
    - Scorable
    - Mobile Friendly

  # REQUIRED. List of Strings. What question types can be imported.
  # Supported Options: `Question/Answer` and `Multiple Choice`.
  supported_data:
    - Question/Answer

  # REQUIRED. Paragraph. Long description displayd on the widget detail page.
  about: >
    In Crossword, fill in the blank squares
    with (a) words based on the clues provided
    in the text and/or (b) by the letters
    overlapping from other words.

  # REQUIRED. Paragraph. Short description shown in catalog.
  excerpt: >
    A quiz tool that uses words and clues to
    randomly generate a crossword puzzle.
```

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
