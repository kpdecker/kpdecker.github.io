---
layout: post
title: Firefocus
date: 2009-11-01 22:19:01.000000000 -06:00
categories:
- Firefocus
tags:
- Firebug
- Firefocus
- Web Development
---

As part of my day job I recently needed to debug focus handling within a AJAXy web app and found that logging and visualizing of this was quite difficult. To assist with this task I spent some of my personal time developing Firefocus, a Firebug extension for tracking keyboard focus at the HTML node level within Firefox.

Firefocus provides the ability to log all focus and blur events to the console as well displaying the element which currently has focus on the HTML panel, particularly helpful for tracking down the nuisance element that managed to inject themselves in the tab order but are not displaying the focus status properly.

This extension supports Firebug 1.4 and higher and is available [here](/firefocus).
