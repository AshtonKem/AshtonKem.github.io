---
layout: page
title: ""
date: 2013-07-12 20:40
comments: false
sharing: true
footer: true
---

# Experience

## Edmunds (2017 - Current)

Edmunds.com is one of the world's premier car research and buying websites. First created in 1966, Edmunds' main goal has been making the car buying process easier for the consumer. Based out of beautiful Santa Monica California, Edmunds has consistently been rated one of the best places to work in LA. It also has an [amazing office](https://www.youtube.com/watch?v=1aq5T1GCyOA) which we are proud of.


At Edmunds I am a tech lead, which means that I lead a small team of 5. I am responsible for the mission critical leads system, which allows Edmunds to take customers interested in a vehicle and transmit this information to the dealeships to start the sales process. The leads system is a critical part of our Tier 3 portfolio, which directly connects Edmunds.com users with dealerships.

Currently we maintain a small suite of Java & Spring based web applications. Our primary goal is to reduce technical debt, and modernize the system with better techniques, tools, and architectures.


## DRW Trading (2015 - 2017)

DRW trading is one of America's largest principle trading firms. DRW participates in a very wide range of market activities including market making, investment, crypto currency exchange, VC investment, and real estate.

At DRW I was a senior engineer on the Trading Infrastructure team. TI was responsible for maintaining two mission critical tools: the reference data store and the position store.

The reference data store contained all relevant details of every single instrument that DRW had ever traded, and then some. This work involved both understanding a wide range of financial products as well as being able to capture the commonalities of instruments offered by different exchanges. We also supported a large number of applications to provide instrument specific calculations, such as option expiration, bond accrued interest calculation, as well as batch jobs to regularly import and process exchange instrument data.

The trade and position data store involved tracking every single trade made in the organization, and keeping an up to the date record of every single trader's position. This involved verifying every trade, providing a way for errors to be easily corrected, as well as providing the ability for desks to search for and view their trades/positions in a real time format. This data had to be accumulated firm wide for regulatory reasons, as well as segregated out so that nominally competitive desks could not see each other's positions.



## Pivotal Labs (2013 - 2015)

Pivotal Labs is a consultancy firm primarily based out of San Francisco, with satellite offices in a large number of major metropolitan hubs world wide. They offer consultancy services to established companies and startups alike, using techniques like pair programming, agile project management, and TDD to show firms how to work better.

At Pivotal I worked in a satellite office on their [Pivotal Tracker](http://www.pivotaltracker.com) product. Pivotal Tracker is an agile project management tool beloved for its opinionated simplicity. Tracker is used universally inside Pivotal and with Pivotal clients, as well as a large number of companies large and small in order to easily track their project's progress.

At Pivotal I basically fulfilled two roles: backend engineer and frontend engineer.

The backend of Pivotal Tracker was a heavily customized Ruby on Rails app providing both static management pages, APIs, a rich event log & caching infrastructure, and a heavily customized documentation system. I was involved in adding features, fixing bugs, and overhauling the search system for the backend.

The frontend of Pivotal Tracker was a Single Page Application (SPA) written in Backbone.js. Well, it is one now, back when I worked there were actually two: classic and next. Another engineer and I spearheaded the project to completely separate the then conjoined classic & next code, fix a large number of show stopping bugs, and eventually release the next codebase out into the wild.

While at Pivotal I also maintained our CI solutions in Jenkins & CircleCI, and also ran an experiment in using generative based testing to search for hard to reproduce bugs. The latter eventually produced a [Clojure Conj](https://www.youtube.com/watch?v=HXGpBrmR70U) talk in 2014.

## Narrative Science (2012 - 2013)

Narrative Science is an AI & Natural Language processing startup based in Chicago Illinois. Their primary product is an algorithm that combines structured data and configuration to produce natural sounding text. There is a high probability that you have read some work from Narrative Science, especially if you are into baseball, stock markets, or sales reports.

Narrative Science was effectively divided into three sections: product, editorial, and data. Product worked on internal tools, editorial provided the configuration required to make the product work, and data provided the structured data required by editorial. I worked on the data team.

While on data, I was responsible for maintaining the ELTV processes for a number of external clients. This would involve downloading files from FTP, extracting any data, cleaning it up automatically, and storing it for future use. We also maintained the monitoring for all of these processes, including alerting when the client had not posted files within the expected time period.

Aside from data, I was involved in Narrative Science's one customer facing product: [Quill for Google Analytics](https://quillengage.narrativescience.com/), which is now called QuillEngage. Quill was a tool that allowed any user to sign in with their Google account, authorize us to view their Google Analytics data, and produce a weekly report summarizing their current trends in natural language. Since Quill was our only user facing product, it had a completely unique front end web application, run using Python in the Google Compute Engine.

## OnShore Development (2011 - 2012)

OnShore is a very small web shop that produces and maintains one product: WebCheckout. WebCheckout is designed to allow arts schools to easily loan out their equipment to their students, track the status of their inventory, and assess fees on students for late returns or damaged equipment. 

WebCheckout is interesting in that it's written in Common Lisp. Large sections of the stack are either completely custom, or heavily customized from what is available in the Open Source community. This meant that when issues arose, often the only people capable of helping us were already debugging the problem. I assume that I must have been one of maybe 1,000 professional Common Lisp developers in the world when I worked at OnShore.

During my time there I was involved in three major projects. The first was to create an internationalization and localization library to prepare the product for sale in the international market. The second was a rewrite of the core scheduling algorithm, reducing some complicated scheduling requests down from an 18 minute average to a 30 second average. And the final was an attempt to rewrite the overly complicated web object based framework to something based on MVC.

# Volunteering
## Care For Real (2012 - 2014)
Loyola University Chicago has a large focus on social justice, and required that most students do volunteering of some sort. I ended up volunteering at a food pantry a quarter mile from campus called Care For Real.

Originally I was going to involved in moving heavy items and handing out food, two activities that ideally happen often at a food pantry. But once they found out I knew computers, plans quickly changed to me overhauling their records system.

Food pantries need to keep track of their clients and what they give out with a ton of paperwork. Making sure that every client is sufficiently fed, that resources are equitably used, and that the pantry is properly supplied by the city's food depository is a task that produces a ton of paperwork. Add on top of that regular interviews to check up on the client's health and to refer people to specialist services, all of which produced even more paperwork.

I was tasked with digitizing this system. First we created an Access 2007 system backed by ODBC and MySQL, since there already were volunteers in the system who knew how to use that. Once this system eventually hit its limit, I ended up creating a completely custom Ruby on Rails web application that could be hosted internally to help streamline the client check in process. We even integrated a receipt printer, so that we could hand clients a receipt for what items they should receive in the back. This helped a *lot* with clients who were not fluent in English.

While I think the system that we created was great, it is sadly no longer in use. The Greater Chicago Food Depository ended up forcing pantries to use their system in order to receive food from the depository. And while I think this is unfortunate, at least the clients of Care For Real are still getting the critical food & services they need.


# Education
## Loyola University Chicago (2008 - 2012) 

I graduated with a B.S. in Computer Science and a B.S. in Mathematics, magna cum laude.

