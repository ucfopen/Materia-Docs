---
title: WIGT Package Structure
tagline: How a .wigt package is Structured
class: developers
category: widgets
---

# Wigt Package Structure Specfics

Wigt packages have rigid file structure requirements.

<pre>
├── /_score-modules
│   └── /<a href="score-module.html">score_module.php</a>
├── /assets
├── /img
│   ├── /screen-shots
│   │   ├── 1-thumb.png
│   │   ├── 1.png
│   │   ├── 2-thumb.png
│   │   ├── 2.png
│   │   ├── 3-thumb.png
│   │   └── 3.png
│   ├── icon-60.png
│   ├── icon-60@2x.png
│   ├── icon-92.png
│   ├── icon-92@2x.png
│   ├── icon-275.png
│   ├── icon-275@2x.png
│   ├── icon-394.png
│   └── icon-394@2x.png
├── creator.html
├── <a href="question-structures.html#demojson">demo.json</a>
├── <a href="install-yaml.html">install.yaml</a>
├── player.html
└── scoreScreen.html
</pre>

## Display Icons

This folder should contain the icons for your widget. A total of four icons at various pixel sizes are required:
* icon-60.png
* icon-60x2.png
* icon-92.png
* icon-92x2.png
* icon-275.png
* icon-275x2.png
* icon-394.png
* icon-394x2.png

## Screen Shots

This folder should contain screen shots and corresponding thumbnails for your widget.  These will be used in the detail page for your widget.

* 1-thumb.png
* 1.png
* 2-thumb.png
* 2.png
* 3-thumb.png
* 3.png
