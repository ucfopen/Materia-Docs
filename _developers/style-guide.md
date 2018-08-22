---
title: Style Guide & Code Linting
tagline: Formatting guide for contributing code
class: developers
category: contributing
---
# Code style guide

Any code intended to be contributed into Materia should follow the Materia coding style guide. We recommend that you use our fork of the PHP Code Sniffer Fuel PHP Standard repository which will enforce our coding standards for any PHP code. If you develop with the Sublime Text editor then you'll also want to get the PhpCS plugin which will display Code Sniffer warnings.

## PHP Style Guide by Example

Look at the comments in the example below which covers all of our PHP coding style guide rules:

```php
	<?php
	// (File names should be all lower case. Files should be in UTF-8 encoding.)
	// (Line endings should be Unix-style LF (\n, not \r\n).)

	// Class names capitalize each word and separate words with underscores:
	class My_Class
	{
	// <-- Indentation should be provided with tabs, not spaces

		// Constants should be written in ALL_UPPERCASE_WITH_UNDERSCORES:
		const DEFAULT_VALUE = 0;

		// Alignment should be provided with spaces and each variable
		// should be declared on it's own line:
		protected $x = 5;
		protected $foo_bar = false; // <-- Variables should be in snake_case

		// Methods should always declare their visibility (public, protected, private)
		// and should be written in snake_case:
		public function is_zero($number)
		{
			$number = (int)$number; // Keywords (int, array, etc) should be lowercase

			// Control flow structures should provide a space between the keyword
			// and arguments (if, for, foreach, while, switch):
			if ($number == 0)
			{
				// Keywords like true, false, null, etc should also be lowercase:
				return true;
			}

			return false;
		}

		public function get_term_id($semester)
		{ // <-- All braces should always be presented on their own line.
			$id = 0;

			switch ($semester)
			{
				// (Each case block is separated from each other by an empty line.)
				// Case statements are indented:
				case 'Fall 2012':
					$id = 1;
					break;

				case 'Spring 2013':
					$id = 2;
					break;
			}

			return $id;
		}

		public function do_something($number)
		{
			// ! should be surrounded by spaces:
			if ( ! $this->foo_bar)
			{
				// Provide spaces before and after logical and math operators:
				return $number + 5;
			}

			return 'hello'; // <-- Always single quote strings if possible
		}
	}

	// Braces for empty classes should be touching and should exist on the same line
	// as the class definition:
	class My_Empty_Class {}

	// <-- Closing ?> tag should be omitted
```

# PHP Code Sniffer Git Hook

PHP Code Sniffer is installed with Composer and included in the dev dependencies of Materia Server.  TWe also install git hooks using composer's `post-install-cmd` script to run tests and run the sniffer.

Basically, if you install Materia Server normally using Composer, then you should be all set.

For detailed information see our [Github PHPCS rules for Materia and FuelPHP](https://github.com/ucfcdl/fuelphp-phpcs)
