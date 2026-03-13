---
title: Embedding Widgets in Canvas
tagline: Using Materia as an Instructor
class: instructors
category: platform
highlighter: no
---
# Overview
Materia integrates into any LMS that supports LTI assignments. This page provides a step-by-step guide on using a Materia widget in your [Instructure Canvas](http://www.instructure.com/) course.

## Using a widget as a graded assignment
This method will put your Materia widget directly into your Canvas course as an assignment. Students will click on the assignment, complete the widget, and scores will pass to your course automatically without students needing to log into Materia.

{% include figure.html
	url="lti-assignment-1.png"
	alt="Creating a new assignment."
%}

0. Navigate to the *Assignments* section.
0. Create a new assignment.

{% include figure.html
	url="lti-assignment-2.png"
	alt="Giving the new assignment a name and score."
%}

<ol>
	<li value="3">Set the name of the assignment.</li>
	<li value="4">Set the number of points for the assignment.</li>
</ol>

> Materia Scores range from 0%-100%, which is multiplied by the points you assign here. (example: If a student scores a 50% and you specify 20 points for this assignment in Canvas then they will receive 10 out of a possible 20 points).

{% include figure.html
	url="lti-assignment-3.png"
	alt="Setting submission type for the assignment."
%}

<ol id="identical">
	<li value="5">Set the submission type to <strong>External Tool</strong>.</li>
	<li value="6">Click <strong>Find</strong> to display the list of available external tools.</li>
</ol>

{% include figure.html
	url="lti-assignment-4.png"
	alt="Setting up the assignment to be a Materia widget."
%}

<ol>
	<li value="7">Select the Materia option (in our example, <strong>Materia Widget</strong>).</li>
</ol>

> If you don't see Materia in this list, then it hasn't been installed into your Canvas instance. If you're managing your own Materia instance then read <a href="{{ site.baseurl }}/develop/system-integrations.html">Systems Integrations</a> to set this up. Otherwise, you'll need to help from your Materia system administrators.

{% include figure.html
	url="lti-assignment-5.png"
	alt="Selecting a widget to use for the assignment."
%}

<ol>
	<li value="8">Now select the Materia widget you’d like to use by clicking <strong>Use this widget</strong> next to the desired widget. If you need to create a widget, you can click on  <strong>create a new widget at Materia</strong> at the bottom of the page, then return to this page to select it.</li>
	<ul>
		<li>
			If you're creating a new widget, you won't see it in this list until you click <strong>Refresh listing</strong>.
		</li>
	</ul>
</ol>

{% include figure.html
	url="lti-assignment-5a.png"
	alt="The selected widget will show linkage progress."
%}

{% include figure.html
	url="lti-assignment-6.png"
	alt="Selecting which widget to use in the course."
%}

<ol>
	<li value="9">Once the widget is linked to the assignment, the widget selection interface should disappear automatically. Click <strong>Select</strong> to finalize the selection.</li>
</ol>

{% include figure.html
	url="lti-assignment-6a.png"
	alt="The selected widget is successfully linked to the assignment."
%}

> If the course containing the linked Materia Widget is copied or moved, the LMS may reset this selection.

{% include figure.html
	url="lti-assignment-7.png"
	alt="Saving the new assignment."
%}

<ol>
	<li value="10">Click <strong>Save</strong> to save the assignment.</li>
</ol>

{% include figure.html
	url="lti-assignment-8.png"
	alt="Checking the new assignment."
%}

<ol>
	<li value="11">Click the assignment to make sure the widget was linked correctly.</li>
</ol>

{% include figure.html
	url="lti-assignment-9.png"
	alt="Confirming the widget was successfully linked to the assignment."
%}

Once you’ve selected a widget, you’re done with creating your assignment.  You can easily change the linked widget at any time.

## Using a widget in a module

There is another way to use Materia in Canvas if don't want to pass scores back as an assignment. This is perfect for situations where you just want to add a study tool into the content of your course.

{% include figure.html
	url="lti-module-1.png"
	alt="Creating a new Materia widget module"
%}

0. First, navigate to the Modules section.
0. Click **Add item to module** (you may need to create a module first).

{% include figure.html
	url="lti-module-2.png"
	alt="Screenshot showing Canvas LTI module item in step 5"
%}

<ol>
	<li value="3">Select <strong>External Tool</strong>.</li>
</ol>

The rest of the process is identical to creating an assignment [starting from step 5](#identical).
