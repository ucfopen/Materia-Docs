---
title: Widget Install.yaml
tagline: Widget Install Configuration
class: developers
category: widgets
highlighter_langs:
  - yaml
---
# Materia Widget Install Configuration

Every widget needs an `install.yaml` file which describes the various settings that will be put into your Materia database to describe that widget.

Here is an example install.yaml file from the Crossword widget:

```yaml
---
general:
  # REQUIRED. String. Name of this widget (spaces and dashes ok).
  name: Crossword

  # REQUIRED. Int. Pixel height of the iframe. Use `0` to make the player width responsive.
  height: 573

  # REQUIRED. Int. Pixel width of the iframe. Use `0` to make the player height responsive.
  width: 715

  # REQUIRED. Boolean. Widget is shown in the highlight catalog.
  in_catalog: Yes

  # REQUIRED. Boolean. `No` prevents the widget from being played.
  is_playable: Yes

  # REQUIRED. Boolean. Enables edit instance and save as draft buttons.
  is_editable: Yes

  # REQUIRED. Boolean. Does this widget store tabular data using the Storage Manager?
  is_storage_enabled: No

  # OPTIONAL. Boolean. Does this widget use AI generation features?
  is_generable: Yes

  # REQUIRED. Int. Version of the Materia API this widget uses. Use `2`.
  api_version: 2

  # REQUIRED. Boolean. Not used
  is_qset_encrypted: Yes

  # REQUIRED. Boolean. Not used
  is_answer_encrypted: Yes

files:
  # REQUIRED. String. Name of the the player html file
  player: player.html

  # OPTIONAL. String. Name of the creator html file. Can use `default` for default creator.
  creator: creator.html

  # REQUIRED. Int. Flash player version required for the player and creator. Not used
  flash_version: 0

score:
  # REQUIRED. Boolean. Does this widget score?
  is_scorable: Yes

  # OPTIONAL. String. Name of the Class in the score module
  score_module: Crossword

  # OPTIONAL - name of the custom score screen html file. Omit to use default score screen
  score_screen: scorescreen.html

meta_data:
  # REQUIRED. List of Strings. What feature tags should be displayed on the widget detail page
  # If empty, set to an empty array - `features: []`
  # NOTE: These will be displayed in the widget catalog and are used as search filters
  features:
    - Customizable
    - Scorable
    - Mobile Friendly

  # REQUIRED. List of Strings. What question types can be imported.
  # Supported Options: `Question/Answer` and `Multiple Choice`, and custom types
  # If empty, set to an empty array - `supported_data: []`
  # NOTE: These will be displayed in the widget catalog and are used as search filters
  supported_data:
    - Question/Answer

  # REQUIRED. Paragraph. Long description displayed on the widget detail page.
  about: >
    In Crossword, fill in the blank squares
    with (a) words based on the clues provided
    in the text and/or (b) by the letters
    overlapping from other words.

  # REQUIRED. Paragraph. Short description shown in catalog.
  excerpt: >
    A quiz tool that uses words and clues to
    randomly generate a crossword puzzle.

  # OPTIONAL. Denotes the repository update type. `github` is the only supported option. Use in conjunction with the `repo` property.
  update_method: github

  # OPTIONAL. Denotes the repository path. Use in conjunction with the `update_method` property.
  repo: ucfopen/crossword-materia-widget

  # OPTIONAL. These enable the widget catalog and detail pages to communicate your widget's accessibility status.
  accessibility_keyboard: Full # Allowed values are `Full`, `Limited`, or `None`
  accessibility_reader: Full # Allowed values are `Full`, `Limited`, or `None`
  accessibility_description: >
    Special navigation hotkeys are utilized. These
    hotkeys are communicated visually and to the screen reader.

  # OPTIONAL. The prompt to be used as part of question generation. Provides more guidance about the structure of the demo json.
  # Requires `is_generable` to be enabled.
  generation_prompt: >
	The resulting json object must contain an items array nested within a second items array within the qset->data object. The data object should additionally contain an options key with two properties: freeWords and hintPenalty. The default values for these two properties are 1 and 50 respectively. The hint value present in each question's options object should provide slightly more information or context to help students guess the associated word, and be more substantive than simply offering the first letter.
```
