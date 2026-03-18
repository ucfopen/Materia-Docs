---
title: Style Guide & Code Linting
tagline: Formatting guide for contributing code
class: developers
category: contributing
highlighter: yes
---
# Code style guide


Any code intended to be contributed into Materia should follow the Materia coding style guide. We require the use of `black`, `flake8`, and `isort` on any code contributions.

Flake8 and isort can be configured to run as pre-commit hooks in git. To do so, run `make dev-setup`. Black can be installed as a VSCode plugin via `Black Formatter` that will format code on save.
