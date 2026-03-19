---
title: Materia v11 Overview
tagline: A Foundational Rebuild of Materia
class: admin
category: v11_upgrade
---

## Server Application Rewrite

The fundamental change for Materia v11 is a transition from PHP and the <a href="https://fuelphp.com/" target="_blank">FuelPHP framework</a> to python and the <a href="https://www.djangoproject.com/" target="_blank">Django framework</a>. This update aligns
Materia's codebase with our other internal tools, and gave us an opportunity to address technical debt that had accumulated since we first launched
Materia in 2012.

## A Better API

Materia's old API was a messy patchwork, and did not properly conform to modern API standards. The rewrite gave us an opportunity to completely rebuild the
API from the ground up, employing proper RESTful design.

## Transition to LTI 1.3

Materia uses the <a href="https://www.imsglobal.org/spec/lti/v1p3/" target="_blank">Learning Tool Interoperability (LTI) specification</a> as defined by <a href="https://www.1edtech.org/" target="_blank">the 1EdTech Consortium</a> (previously IMS Global) to communicate with Learning Management Systems. We previously utilized the LTI 1.1 specification, which has been deprecated in favor of the newer, more robust, more secure 1.3 specification. Note that with Materia v11, we <strong>no longer support LTI 1.1</strong>. This necessitates new configurations in your LMS to integrate Materia.

## Thoughtful LMS Integration

We took the opportunity to smooth out some of the roughness of our LMS integration. This includes better awareness of course and institutional roles when authenticating with LTI, 
which means instructors will no longer flip back and forth between Staff and Student depending on the context of their last authentication. In addition, we've made a fundamental change
to how Materia recognizes course owners when assessing widget access: course owners will now be granted automatic, provisional access to instances they don't own when they visit them 
in their course.

Lastly, we took the opportunity to polish our course navigation landing page, and added contextually relevant content depending on the user's role (staff or student) in the course.

## Improved Widget Management

A long-desired feature for us was a better way of managing Materia's currently installed suite of widget engines, and making the update process more straightforward. With Materia v11, open-source widgets can now include metadata that enables Materia to track the currently installed version, and easily check for new updates.

## Better Theming

We've built some improved tooling for customizing your instance of Materia with information relevant to your own institution. It's now easier to define custom strings and override specific components in React to make Materia yours.
