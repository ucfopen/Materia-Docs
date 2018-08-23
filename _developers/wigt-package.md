---
title: WIGT Package Structure
tagline: How a .wigt package is Structured
class: developers
category: widgets
---

# Wigt Package Structure Specfics

Wigt packages have rigid file structure requirements so that the installer knows where to find every assets.

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
├── <a href="widget-developer-guide.html#the-creator">creator.html</a>
├── <a href="question-structures.html#demojson">demo.json</a>
├── <a href="install-yaml.html">install.yaml</a>
├── <a href="widget-developer-guide.html#the-player">player.html</a>
└── <a href="widget-developer-guide.html#the-score-screen">scoreScreen.html</a>
</pre>



## Display Icons

This folder should contain a square icon for your widget.  The icon requires 4 sizes with 2 pixel densities, together totaling 8 files.

![Icon]({{ site.baseurl }}/assets/img/widget-icons/icon-394.png)
![Icon]({{ site.baseurl }}/assets/img/widget-icons/icon-275.png)
![Icon]({{ site.baseurl }}/assets/img/widget-icons/icon-92.png)
![Icon]({{ site.baseurl }}/assets/img/widget-icons/icon-60.png)

> Download the set of [default Materia widget icons]({{ site.baseurl }}/assets/img/widget-icons/default-materia-widget-icons.zip)


## Screen Shots

This folder should contain **three** screen shots (with thumbnails).  These will be used on the widget about page.

{% include figure.html
	url="widget_detail.png"
	alt="The widget detail page displaying 3 screen shots."
%}
