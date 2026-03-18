---
title: ScoreModule Class
tagline: Scoring submissions on the server is the safest way
class: developers
category: widgets
---
# Score Module

If you set `is_scorable` in [install.yaml](install-yaml.html) to 'yes', a score module will be required. Drop `src/_score/score_module.py` in your source code and customize it as needed.

The score module is the single server-side element of a Materia widget. It processes raw play logs and computes the score the user should receive for a particular play session. Additionally, score modules format data for the score screen. Score modules are designed to be flexible and extensible: while there are a number of base score module methods that can be overridden, only one is _required_: the `check_answer(self, log)` method.

The `score_module.py` file is a python class which extends `ScoreModule`.  Your score module should override the `check_answer(self, log)` method at minimum.  Your implementation of this method should return a number between 0-100 representing the score for the given question response.  The `log` object contains data for the current submission log being evaluated.

## Basic score module example

```python
from scoring.module import ScoreModule


class MyWidget(ScoreModule):

    def check_answer(self, log):
        question = self.get_question_by_item_id(log.item_id)
        if not question:
            return 0

        user_text = str(log.text).strip()
        for ans in question.get("answers", []):
            if user_text == str(ans.get("text", "")).strip():
                try:
                    return int(ans.get("value", 0))
                except Exception:
                    return 0
        return 0

```

Let's break down what's happening here: `check_answer(self, log)` is called for every log that's submitted during the play session of type `SCORE_QUESTION_ANSWERED`, which is the vast majority of logs for Materia widgets. These are submitted by the widget player when a user responds to a "question", whether that question is well-defined (a multiple-choice question, for example) or more loosely defined (a label being dragged to a particular anchor on an image.)

Logs contain several fields: `item_id`, `text`, and `value`. `item_id` should be a reference to the ID of the question in the qset, so the module can deterministically map a log response to a question in the qset. `text` and `value` are generic fields that can be populated with whatever information the score module needs to identify and assess the appropriate score for the given log submission.

In this example, the current log is provided by the `log` parameter. The base score module has a helper method `self.get_question_by_item_id()` to locate the `Question` model instance associated with the current qset that possesses the associated `item_id`.

> `self.get_question_by_item_id()` relies on Materia correctly recognizing individual atomic "questions" in your widget's qsets so it can generate `Question` model instances when the qset is first saved. If `self.questions` is an empty or incomplete list, `self.qset` can be traversed manually to locate whatever information is required.

It's a good idea to include an error boundary if `question` is not available, which this score module includes by returning `0`.

Next, this `check_answer()` method is comparing the log text to the correct answer text in the qset for the associated question. If the text matches, the `value` property of the answer is returned.

## Optional Score Module Methods

The score module has additional methods that can be overridden, which may be necessary depending on whether your widget is submitting additional log types or the score screen has extra data requirements. Let's review a few of the important ones:

#### `handle_log_widget_interaction(self, log)`

This method handles `SCORE_WIDGET_INTERACTION` log types, which typically involve some form of score modifier. For example, asking for a clue for a particular question may reduce the score value of a question by a certain amount.

---

#### `handle_log_client_final_score(self, log)`

This method handles `SCORE_FINAL_FROM_CLIENT` log types. These generally supersede or override the aggregate score from individual responses. For example, the Adventure widget does not score users based on individual nodes they traverse (but those traversals are still logged via `SCORE_QUESTION_ANSWERED`): the score is entirely determined based on where the user ends up, which is reported with a `SCORE_FINAL_FROM_CLIENT` log submission.

---

#### `handle_log_question_answered(self, log)`

This is the parent method to `check_answer(self, log)`. Your widget may need to override this log handler method if you are calculating the score in a special manner that is different from the default.

---

#### `get_overview_items(self)`

This method builds the individual line items that comprise the score breakdown in the score overview section of the score screen. You may need to override this method if your widget's score calculation is more complex than _`initial_score - points_list = final_score`_.

---

#### `get_score_details(self)`

This method builds the score table that is passed to the score screen. If your widget implements a custom score screen, you may choose to alter the data provided to it by overriding this method.

---

#### `details_for_question_answered(self, log)`

This method is called by `get_score_details` to assemble data for each row of the score table. If you are overriding `get_score_details()`, chances are you may need to override this method as well.

