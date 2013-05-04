---
layout: post
title: "Introducing Butler.el"
date: 2013-05-03 20:00
comments: true
categories: [ programming, emacs, open-source, release ]
---


I'm a big fan of reducing barriers to increase adoption and compliance. One of the largest examples for me in the past few months was when I tried out Eclipse for Python Development. The most important plugins for me (other than Python and Git) were the Jenkins and Jira plugins. Quick access to my tasks and instantaneous in-editor feedback about job status increased the likelihood that I would respond to failed builds or fill out tickets correctly.

I eventually decided that Eclipse was overkill for Python development, and way too heavy for a Macbook Air. But in my switch back to Emacs I particularly missed my Jira and Jenkins plugins. This is why I'm writing to announce the 0.1.0 release of [Butler](https://github.com/AshtonKem/Butler). Butler allows for you to view the jobs on your Jenkins servers, and trigger new jobs without switching out of Emacs.

At this point Butler's feature set is modest: view, refresh, and trigger. These are just the groundwork functions for a larger feature set in the coming weeks, including console output, visual indication of running jobs, watching jobs, build queue, etc. I have high hopes that in a few months this plugin will be as mature as its eclipse counterpart.

If you have any bugs or feature requests, please add them to the Github issue tracker.
