---
layout: post
title: What’s your signal-to-noise ratio in your code?
date: 2012-09-24 09:21
author: cyrille martraire
comments: true
categories: [Bonnes pratiques de dév, communication, DDD, DDDx, domain, Domain-Driven Design, intentionrevealing, noise, physics of software, Programmation, signal, snr, ubiquitouslanguage]
---
<p style="text-align: justify;">You write code to deliver business value, hence your code deals with a business domain like e-trading in finance, or the navigation for an online shoe store. If you look at a random piece of your code, how much of what you see tells you about the domain concepts? How much of it is nothing but technical distraction, or « noise »?</p>

<h1 style="text-align: justify;">Like the snow on tv</h1>
<p style="text-align: justify;">I remember TV used to be not very reliable long ago, and you’d see a lot of « snow » on top of the interesting movie. Like in the picture below, this snow is actually a noise that interferes with the interesting signal.</p>

<div id="attachment_3521" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/tv_snow.jpg"><img title="tv_snow" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/tv_snow.jpg" alt="TV signal hidden behind snow-like noise" width="240" height="187" /></a>TV signal hidden behind snow-like noise

</div>
<p style="text-align: justify;">The amount of noise compared to the signal can be measured with the <strong>signal-to-noise ratio</strong>. Quoting the definition from Wikipedia:</p>

<blockquote><a href="http://en.wikipedia.org/wiki/Signal-to-noise_ratio" target="_blank">Signal-to-noise_ratio</a> (often abbreviated <strong>SNR</strong> or <strong>S/N</strong>) is a measure used in science and engineering that compares the level of a desired signal to the level of background noise. It is defined as the ratio of signal power to the noise power. A ratio higher than 1:1 indicates more signal than noise.</blockquote>
<p style="text-align: justify;">We can apply this concept of <strong>signal-to-noise ratio</strong> to the code, and we must try to maximize it, just like in electrical engineering.</p>

<h1 style="text-align: justify;">Every identifier matters</h1>
<p style="text-align: justify;">Look at each identifier in your code: package names, classes and interfaces names, method names, field names, parameters names, even local variables names. Which of them are meaningful in the domain, and which of them are purely technicalities?</p>
<p style="text-align: justify;">Some examples of class names and interface names from a recent project (a bit changed to protect the innocents) illustrate that. Identifiers like « CashFlow »or « CashFlowSequence » belong to the <a href="http://martinfowler.com/bliki/UbiquitousLanguage.html" target="_blank">Ubiquitous Language</a> of the domain, hence they are the <strong>signal</strong> in the code.</p>

<div id="attachment_3523" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/snr_ubiquituous_language.png"><img title="snr_ubiquituous_language" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/snr_ubiquituous_language.png" alt="Examples of classnames as signals, or as noise" width="533" height="247" /></a>Examples of classnames as signals, or as noise

</div>
<p style="text-align: justify;">On the other hand, identifiers like « CashFlowBuilder » do not belong to the ubiquitous language and therefore are <strong>noise</strong> in the code. Just counting the number of « signal » identifiers over the number of « noise » identifiers can give you an estimate of your signal-to-noise ratio. To be honest I’ve never really counted to that level so far.</p>
<p style="text-align: justify;">However for years I’ve been trying to maximize the signal-to-noise ratio in the code, and I can demonstrate that it is totally possible to write code with very high proportion of signal (domain words) and very little noise (technical necessities). As usual it is just a matter of personal discipline.</p>
<p style="text-align: justify;">Logging to a logging framework, catching exceptions, a lookup from JNDI and even @Inject annotations are noise in my opinion. Sometimes you have to live with this noise, but everytime I can live without I definitely chose to.</p>

<h1 style="text-align: justify;">For the domain model in particular</h1>
<p style="text-align: justify;">All these discussion mostly focuses on the domain model, where you’re supposed to manage everything related to your domain. This is where the idea of a signal-to-noise ratio makes most sense.</p>

<h1 style="text-align: justify;">A metric?</h1>
<p style="text-align: justify;">It’s probably possible to create a metric for the signal-to-noise ratio, by parsing the code and comparing to the ubiquitous language « dictionary » declared in some form. However, and as usual, the primary interest of this idea is to keep it in mind while coding and refactoring, as a direction for action, just like test coverage.</p>

<address>I introduced the idea of signal-to-code ratio in my talk at DDDx 2012, you can watch <a href="http://skillsmatter.com/podcast/design-architecture/applying-ddd-legacy-app" target="_blank">the video here</a>. Follow me (<a href="https://twitter.com/" target="_blank">@cyriux</a>) on Twitter!</address>
<h3 style="text-align: justify;">Credits:</h3>
<p style="text-align: justify;">TV noise picture: <a href="http://creativecommons.org/licenses/by-nd/2.0/" target="_blank">Some rights reserved</a> CC par <a href="http://www.flickr.com/photos/28471130@N07/">massimob(ian)chi</a></p>
