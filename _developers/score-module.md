---
title: ScoreModule Class
tagline: Scoring submissions on the server is the safest way
class: developers
category: widgets
---
# Score Module

If you set `is_scorable` in [install.yaml](install-yaml.html) to 'yes', a score module will be required. Drop `src/_score/score-module.php` in your source code and customize it as needed.

The `score_module.php` file is a PHP class which extends `Score_Module`.  Your score module should override the `checkAnswer` method.  Your implementation of this method should return a number of 0-100 representing the score for the given question response.  The `$log` object contains any data saved to the server by your widget (usually question or performance data).

## Basic score module example

```php
<?php
namespace Materia;

class Score_Modules_MyWidget extends Score_Module
{
	public function check_answer($log)
	{
		if (isset($this->questions[$log->item_id]))
		{
			$question = $this->questions[$log->item_id];
			foreach ($question->answers as $answer)
			{
				if ($log->text == $answer['text'])
				{
					return $answer['value'];
					break;
				}
			}
		}

		return 0;
	}
}
```

This example uses `Score_Module`'s `questions` property which contains a reference to this widget instance's `qSet`.

> Look at the <code>Score_Module</code> class for all of the properties available to you.
