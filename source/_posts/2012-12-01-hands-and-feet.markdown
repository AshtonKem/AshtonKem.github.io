---
layout: post
title: "Hands and Feet"
date: 2012-12-01 21:16
comments: true
categories: [theory]
---


When I was a teenager I worked at a large amusement park in the games department, which I guess technically means I was a carnie. One of the more interesting games I worked at was the rope ladder; in order to win the game contestants had to climb up a rope ladder set at a 45 degree angle and attached to swivel points at the bottom. Unsurprisingly I learned to climb it easily (free tries and boredom does wonders for learning), but I witnessed very few winners.

One of the more important lessons I learned was about how to not think about specific tasks. In order to climb the ladder, you need to move opposite limbs: step forward with your right hand and left foot at the same time, etc. This spreads your weight out, and ensures that any movement you make is relatively counter-balanced, reducing the maximum amount you’ll find yourself unbalanced at a given moment. What was particularly interesting to me was the transition from not-making it, to making it. When I first started, I had to concentrate on my hands and feet, and fell constantly. Only once I learned to move my hands and feet without conscious thought, and thus free up my mind to worry about my balance, did I successfully climb the ladder.

This has fairly important implications to my daily work: namely that I cannot concentrate on doing good engineering work without making everything else a subconscious action. Every single non-work related activity breaks concentration for a short period of time, and potentially might take me out of the elusive “zone”. I’ve discovered that If I’m concentrating on your editor, I’m not going to code effectively (if at all). Therefore, It’s absolutely important that every single editor and environment change that you make is judged by balancing how much time/energy it takes to learn and use against its effect. The most powerful Git extension in the world for your editor will do you no good if it takes 2 minutes to commit each time: it would take less time and mindshare to simply type or alias it out on the shell.

For example I use the [Magit](http://philjackson.github.com/magit/) extension for Emacs to avoid having to go to a terminal to use git; the gains for moderately complicated tasks such as stash manipulation, reverting files, and partially staging a file are truly tremendous. Similarly I use a pylint & nosetests extension for Emacs to avoid having to remember the (quite long) commands it takes to run each command correctly. This also has the tremendous advantage of not linting or testing code that’s outside of my purview, thus running faster and cluttering my mind less with irrelevant results.

The other important take away is to not overdue it. The point of any given addition to your editing environment is to improve the quality and quantity of your engineering output. So if your editor bothers you every 5 minutes with CI notifications that aren’t related to any of your commits, you aren’t really gaining anything. This is why I ended up turning off the Pyflakes/Flymake extension to Emacs: it improved my coding a bit at the time (although most of the time it highlighted code that I hadn’t finished typing yet), but the additional .flymake files it left laying around tripled the amount of time it took for me to work with Git. It simply wasn’t worth the trouble in the long run.

The easiest way to tell whether or not something is worth it is to read the manual and give it a try. Some tools take a while to learn, like Magit, and others only take some time in the setup, like Flymake. Once you’ve exited the learning stage, you should think heavily about whether or not you’re concentrating on the tool instead of your work, and whether or not this has affected the quality or quantity of your output. Remember, there’s no shame in uninstalling a tool if it really doesn’t make your life easier.
