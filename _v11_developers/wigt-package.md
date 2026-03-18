---
title: WIGT Package Structure
tagline: How a .wigt package is Structured
class: developers
category: widgets
---

# Wigt Package Structure Specifics

Materia compiles widget engines into `.wigt` files for easy updates and installation. They contain the widget's front-end source code, metadata, score module, icons, and demo qset. `.wigt` packages have relatively rigid file structure requirements so that the installer knows where to find and install various assets.

```ini
src/
  _score/
    score_module.py
  _icons/
    icon-60.png
    icon-60@2x.png
    icon-92.png
    icon-92@2x.png
    icon-275.png
    icon-275@2x.png
    icon-394.png
    icon-394@2x.png
  _screen-shots/
    1-thumb.png
    1.png
    2-thumb.png
    2.png
    3-thumb.png
    3.png
creator.html
demo.json
install.yaml
player.html
LICENSE
package.json
README.md
webpack.config.js
```

## Display Icons

This folder should contain a square icon for your widget.  The icon requires 4 sizes with 2 pixel densities, together totaling 8 files:

```shell
  _icons/
    icon-60.png
    icon-60@2x.png
    icon-92.png
    icon-92@2x.png
    icon-275.png
    icon-275@2x.png
    icon-394.png
    icon-394@2x.png
```

> The Materia Widget Development Kit has a convenient tool to generate all the required sizes of widget icons quickly. Create an icon of the maximum required size (788x788px) and then select "Generate Icons" from the MWDK landing page.

{% include figure.html
	no_thumb="true"
	url="mwdk-landing-page.png"
	alt="The landing page of the Materia Widget Development Kit."
%}

## Screen Shots

This folder should contain **three** screen shots (with thumbnails).  These will be used on the widget's "about" page.

{% include figure.html
	url="widget_detail.png"
	alt="The widget detail page displaying 3 screen shots."
%}
