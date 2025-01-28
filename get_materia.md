---
layout: default
title: How to get Materia
tagline: Run Materia in minutes.
class: admin
highlighter: no
---

Before anything else, be sure to consult our [Getting Started](admin/getting-started.html) guide for a high-level of how Materia is setup, configured, and run.

# Materia on Github

The repository for the Materia application is on [GitHub](https://github.com/ucfopen/Materia). You can clone it locally or fork it, but whether that's necessary depends on your use case!

#### _I want to use Materia in a production environment at my institution_:

Review the [setting up docker](admin/setup-configure-docker.html) guide for a breakdown of how to setup and configure Materia for production. You don't need the repo!

> You might actually need the repo if you're going to be deploying a custom docker image with your own changes built in. Let's not sweat that just yet though.

#### _I want to setup and run Materia quickly, just to try it out_:

Go ahead and clone the repo, review the [README](https://github.com/ucfopen/Materia?tab=readme-ov-file#setup) and run the `run_first_for_nondev.sh` script as instructed in the README's Setup section.

> Remember that you'll need `yq` installed on your machine prior to running this script. Make sure it's the [right version](https://github.com/mikefarah/yq?tab=readme-ov-file#install) too.

#### _I want to setup Materia for local development_:

Clone the repo, review the README, and run the `run_first_for_dev.sh` script instead.



