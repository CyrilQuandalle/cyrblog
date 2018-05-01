---
layout: post
title: Øredev 2013 – What you probably missed
date: 2013-11-15 17:20
author: cyrille martraire
comments: true
categories: [3 amigos, anarchy, bdd, conference, DDD, Evénements, microservices, mob, oredev, Programmation, radical, speaking, specification]
---
<p style="text-align: justify;">Øredev 2013 was last week, and it was fantastic!</p>

<h1 style="text-align: justify;"><strong>Sharing knowledge</strong></h1>

<p style="text-align: justify;">Øredev is in Malmö, Sweden. It’s very close to Copenhagen, so you can fly to there and then take a 20mn train to arrive in Malmö.</p>

<p style="text-align: justify;">It’s a fantastic conference, totally vendor-neutral (that’s very important). It’s big yet friendly, with a mix of well established topics and more experimental ideas. This year the theme was « The Arts », and as a result it was deliberately provoking or weird in some aspects, and that is a good thing!</p>

<p style="text-align: justify;">For me the highlights of the conference were the radical ideas brought by two guys with some experience in the business: Woody Zuill and Fred George (I’ll come to it in a minute). I also enjoyed a lot how Jessica Kerr <a href="https://twitter.com/jessitron">@jessitron</a> manages to make alternative ways of thinking more accessible and attractive for developer using mainstream languages like Java or C#. Unfortunately I missed <a href="https://twitter.com/bodil">@Bodil</a> talks because the room was too packed to be even able to open the door…</p>

<p style="text-align: justify;">Before the conference you can attend 1-day trainings, and I decided to attend the <em>Value-Driven Product Developmentcourse </em>by JB Rainsberger <a href="https://twitter.com/jbrains">@jbrains</a>. It’s a very good course, more advanced and probably not for beginners. I knew a lot about BDD and has attended other courses already, yet I still learnt a lot during this workshop. I missed other talks from JB, but I want to watch the videos since I had very good feedbacks from other attendees.</p>

<p style="text-align: justify;">It was interesting to listen to experience reports (<em>New Frontiers For In-House Legal Practice</em> by Kate Sullivan, <em>Data @ King – How we are able analyze 100M DAU</em> by Mats-Olov Eriksson, <em>Curiosity killed the cat, but what kills curiosity?by </em>Ann-Marie Charrett, <em>Less is more! – when it comes to art and software, </em>by <a href="https://twitter.com/JimmyNilsson">@JimmyNilsson</a>) with anecdotes and honest accounts of successes, failures and evolutions of mindset.</p>

<h1 style="text-align: justify;"><strong>Radical Ideas</strong></h1>

<p style="text-align: justify;">The Øredev program committee likes to take risks and challenge the way we think about software, as demonstrated by Woody and Fred talks, but also through the talk <em>Code as a crime scene</em> by Adam Petersen Tornhill. Adam tries to reuse forensic methods used for crime investigations to help on large legacy code bases. He built the tool CodeMaat to visualize likely aggressions on the code base based on these ideas.</p>

<p style="text-align: justify;">More radically, Woody Zuill talked about the <strong>Mob Programming</strong> approach his team has been practicing for some time now. He does not claim you should do the same, and he explains that this approach is just the result of doing more of the good things as found during retrospectives. His team found that working together on one task at a time on one single machine at a time was good, so they decided to do that all the time. You must watch his talks: <a href="http://oredev.org/oredev2013/2013/wed-fri-conference/mob-programming-a-whole-team-approach.html">Mob Programming, A Whole Team Approach (Roy « Woody » Zuill)</a>. It includes a time-lapse video and is very interesting. It also challenges the way we think about work. What if what the actual “work” was actually what’s between what we usually call “work”?</p>

<p style="text-align: justify;">Very radical too, Fred Georges <a href="https://twitter.com/fgeorge52">@fgeorge52</a> talked about his approach of <strong>Programmer Anarchy</strong>, “because that’s what it is”. He’s now replicated the experiment at two different companies including a rather traditional one (the Daily Mail newspaper), and is starting again in yet another. Again he does not claim you should do the same, just that it works for them. Again using the power of retrospectives, they got rid of every role except just the customer and the programmer roles. They don’t use the usual software craftsmanship practices like testing and refactoring. However they take great care of the business domain, just like a trader and a developer working closely together can end up giving suggestions to each other, in both ways. As Fred says: “Power to the programmer!”.</p>

<p style="text-align: justify;">This approach works thanks to the use of Micro-Services. This style of architecture in itself is also a bit radical, with a “rapid”, an ordered bus of all the events of the whole system, and a lot of very small, cohesive, disposable micro-services that listen and publish to the bus. You can copy-paste a service to create another, you can rewrite a service rather than make changes, you can plug your new service directly into production! It may sound chaotic but in my opinion this style is disciplined indeed.</p>

<p style="text-align: justify;">Woody gave another talk <a href="https://vimeo.com/79128724">No Estimates: Let’s explore the possibilities (Roy « Woody » Zuill)</a>. It’s really a beautiful talk thanks to the beautiful illustrations from his wife Andrea. Woody does a great job at making us question our need for estimates, what it really means and how it can harm. More importantly, he suggests that estimates are an obstacle against delivering something truly wonderful!</p>

<p style="text-align: justify;">I was lucky to spend some time talking with Woody and Fred, and what they do is very exciting. It’s a paradox, but both still really follow agile values, despite taking huge liberties with respect to the usual principles and practices. Both Fred and Woody also know a lot about object oriented principles and made sure their teams was skilled in that too. However in each case the experiments are also biased because of the very presence of outstanding developers like Fred or Woody!</p>

<h1 style="text-align: justify;"><strong>Testing is not just checking</strong></h1>

<p style="text-align: justify;">Software development requires a mix of many different skills. Some of the important skills revolve around testing. At Øredev you could listen and talk to some of the most notable representatives of the testing community: <a href="http://oredev.org/oredev2013/2013/wed-fri-conference/heuristics-of-testability.html">Heuristics of Testability (James Bach)</a> <a href="https://twitter.com/jamesmarcusbach">@jamesmarcusbach</a>, <a href="https://vimeo.com/79103608">Regression Obsession (Michael Bolton)</a> <a href="https://twitter.com/michaelbolton">@michaelbolton</a>, <a href="https://www.youtube.com/watch?v=-Vc6VszySqY">Balancing ATDD, GUI Automation and Exploratory Testing (Michael Larsen)</a>, (<em>Curiosity killed the cat, but what kills curiosity?</em> by Ann-Marie Charrett). Other talks (<em>The Beauty of Minimizing Effort and Maximizing Creativity While Integrating Performance Throughout the Lifecycle</em> by Scott Barber and <em>The Psychology of Testing, </em> by M isko Hevery) were also about testing.</p>

<p style="text-align: justify;">I realized that testing is much much more than just <em>checking facts</em>. There is a whole universe of testing practices that you are probably not even aware of, and most of this universe cannot and should not be automated.</p>

<h1 style="text-align: justify;"><strong>Software development is a creative job!</strong></h1>

<p style="text-align: justify;">As part of the theme “The Arts” some talks were not about software development. I really loved the talk <a href="http://oredev.org/oredev2013/2013/wed-fri-conference/shakespeare-in-dev.html">Shakespeare in Dev (Thomas Q Brady)</a> and the opening keynote of the second day “<em>The Creativity (R)Evolution</em>” by Denise Jacobs <a href="https://twitter.com/denisejacobs">@DeniseJacobs</a>. Denise managed to trigger the desire to write, talk and share insights from many attendees in the room during her keynote!</p>

<h1 style="text-align: justify;"><strong>My talk</strong></h1>

<p style="text-align: justify;">I was excited to talk at Øredev on Friday after lunch: <a href="https://vimeo.com/79181633">Refactor your specs! (Cyrille Martraire)</a> The room was almost full, which may suggest that the topic is of interest for many. As a speaker I loved the professionalism of the staff doing the video, sound and organization all around so that everything runs smooth for everyone. Thanks a lot to you all! Overall my talk was well received and I had many good questions and very good feedback’s. As I said, this talk is just the beginning of a conversation that will go on, so feel free to contribute.</p>

<p style="text-align: justify;">All the Øredev videos are available on this page: <a href="https://www.youtube.com/watch?v=5cvaCq1q9_E">Mob Programming, A Whole Team Approach - Woody Zuill</a> (still not complete at the time of writing), so have fun and enjoy them all! Also have a look at the #oredev hashtag on Twitter for more quotes, and don’t forget to follow me at <a href="https://twitter.com/cyriux">@cyriux</a> on Twitter!</p>
