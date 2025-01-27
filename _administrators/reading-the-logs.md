---
title: Materia Log Files
tagline: Understanding the Materia Logs
class: admin
category: maintenance
highlighter: no
---
# Reading Materia's Log Files

## Location
Materia uses the standard logging functionality built into FuelPHP.  Logs are written to the `/fuel/app/logs/` directory.  We have several files to keep some logging easier to process, each one following the **file-`YEAR`-`MONTH`-`DAY`.php** format.

> FuelPHP offers several options to control logging, view them [in the FuelPHP log documentation](http://fuelphp.com/docs/classes/log.html).

## General Logs

**materia**: General errors and debug statements are written here.  The Materia debugging function `trace()` will also write to this file.

**exception**: While most potential exceptions will be caught and logged in the `materia` log file, any uncaught exceptions will be written into this file.

## LTI logging

**lti**: Logs from lti messages sent or received.

These logs are structured in a specific format format.  They are comma seperated values, with one log per line.  Each line has the following columns in order:

0. Log Type
0. Instance ID
0. User ID
0. Service Url
0. Score
0. Source ID
0. Unix Timestamp

Possible Log types:

* __session-init__: The consumer has sent a launch message to Materia, and they should be logged in at this point.
* __outcome-no-passback__: Materia was lauched by the consumer, and the user completed a widget, but for some reason, no score was passed back to the   This can happen when the consumer doesn't send a passback url, or when some required data was not found or is not valid.
* __outcome-success__: A score was sent back to the consumer successfully.
* __outcome-failure__: A score was sent back to the consumer.  However, there was a failure when sending or verifying the consumer's response.

**lti-error-dump**: Any errors or exceptions that occur during an LTI request's OAuth validation or grade passback will be written into this file.
