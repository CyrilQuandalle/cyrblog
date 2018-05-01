---
layout: post
title: Visualizing Quality – keynote by Gojko Adzic at BDD Exchange 2011
date: 2011-11-24 11:18
author: cyrille martraire
comments: true
categories: [Actu, alarm, bdd, bddx, Bonnes pratiques de dév, coverage, Evénements, information, metrics, Programmation, programming, quality, testing, visualization]
---
<div>

How do you measure quality?  Number of defects? Customer happiness? Money earned? Developer smiles? That’s the question raised by <a href="http://twitter.com/#%21/gojkoadzic" target="_blank">@gojkoadzic</a> in his keynote at the recent BDD and Agile Testing Exchange in London, to make us think and propose some elements of response.

</div>

<h1>We tend to ignore information</h1>

<div>

We are used to ignore automatic alarms, even fire alarms:  we just don’t care, but we care when a person tells us about an issue or is shouting « fire in the building! ».

</div>

<div>
<div id="attachment_3289"><a href="http://cyrille.martraire.com/wp-content/uploads/2011/11/information1.jpg"><img title="information1" src="http://cyrille.martraire.com/wp-content/uploads/2011/11/information1-225x300.jpg" alt="" width="225" height="300" /></a></div>
As in the story of the Gloves on the Boardroom Table,  making the problem tangible and visible help trigger reactions. The book Switch gives examples of such surprising techniques. Sharing information is not enough, a company is like an elephant, you don’t really drive it, you motivate it to go where you’d like to go. So visualization can help there.

</div>

Best practices change over time, as the techniques in athleticism replace each other. For example at one company they disable most of their functional tests after a feature is developed, because they impede the frequency of their release cycle; on the other hand, they closely monitor the conversion rate of the website, which is for them the best metrics since it encompasses both the technical bugs and the business bugs.

<div>

This does not mean you should disable your functional tests, but it illustrate that what may seem nonsense today may be the next best practice tomorrow.

</div>

<h1>Absence of bugs ≠ Presence of quality</h1>

<div>

The absence of bugs is completely different than the presence of quality. Twitter has lots of bugs but most people are happy with it so it has quality; on the other hand, Nokia phones have no bug, yet if nobody likes them they have no quality either.

</div>

<div>
<div id="attachment_3290"><a href="http://cyrille.martraire.com/wp-content/uploads/2011/11/bug1.jpg"><img title="bug1" src="http://cyrille.martraire.com/wp-content/uploads/2011/11/bug1-300x225.jpg" alt="Onboard screens in a plane all showing a computer crash" width="300" height="225" /></a></div>
So how to measure quality? If you put 20 people in a room with 3 post-it’s each and ask them to write what attributes of the product they think are the most important, and if you get a small enough group of common answers then they are candidates of things you need to measure.

</div>

<div>Usually quality c an be measured at 3 levels (the 3 P’s):</div>

<ol>
    <li>Process effectiveness</li>
    <li>Product status</li>
    <li>Production performance</li>
</ol>

<div>

Here I have the feeling that each of the first two is just a proxy for the next, and only the last is the real one, though I’m not sure.

</div>

<h1>Test coverage is crap</h1>

<div>

Taking an example of a UI, a window with 5 buttons on it it will likely requires 5 time more testing than one with only one button, even if the later brings a lot more value, which suggests that test coverage is crap.

</div>

<div>

If test coverage itself is crap, you may however monitor your <strong>risk coverage</strong>, that is the amount of testing compared to the associated risk. This metrics helps identify where you need to test more.

</div>

<div>
<h1>Visualize to make information actionable</h1>
Once you know what to measure, you want to visualize it. James Bach proposes his technique of low-tech testing dashboard. You divide your system into high-level subsystems, and for each you monitor the « progress of testing »: not yet started, half done or done, and the « level of confidence so far » with 3 smileys from happy to anxious. That’s better than a bug-tracking tool with 500 tickets mixed altogether.

</div>

<div>

Another visualization mode is the use of effects maps, or <a href="http://googletesting.blogspot.com/2011/10/google-test-analytics-now-in-open.html" target="_blank">ACC matrix</a>. You may also monitor in realtime the feed of sentiment of every Twitter message about your company or product as they do at FINN Technology in Oslo.

</div>

<div>

As a conclusion, Gojko introduces the brand new visualisingquality.org website where you can find many ways to visualize your measures of quality and where you can contribute additional ones. Since making things visible is so essential for action, let’s try ideas and share them through this website!

</div>

<h2>References:</h2>

The podcast of the full talk is available here: <a href="http://skillsmatter.com/event/agile-testing/agile-testing-bdd-exchange-2011/js-2990" target="_blank">http://skillsmatter.com/event/agile-testing/agile-testing-bdd-exchange-2011/js-2990</a>
