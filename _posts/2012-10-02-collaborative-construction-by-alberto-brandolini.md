---
layout: post
title: Collaborative Construction by Alberto Brandolini
date: 2012-10-02 10:11
author: cyrille martraire
comments: true
categories: [Actu, Alberto Brandolini, Bonnes pratiques de dév, boundedcontext, collaboration, conflict, DDD, DDDx, deal, domain, Domain-Driven Design, draft, Evénements, immutable, insight, pattern, Programmation, semantics, signed, trade, ux, validation]
---
<p style="text-align: justify;">Alberto Brandolini (<a href="https://twitter.com/ziobrando" target="_blank">@ziobrando</a>) gave a great talk at the last <a href="http://skillsmatter.com/event/design-architecture/ddd-exchange-2012" target="_blank">Domain-Driven Design eXchange</a> in London. In this talk, among many other insights, he described a recurring pattern he had seen many times in several very different projects: « Collaborative Construction, Execution &amp; Tracking. Sounds familiar? Maybe we didn’t notice something cool »</p>

<h1 style="text-align: justify;">Analysis conflicts are hints</h1>
<p style="text-align: justify;">In various unrelated projects we see similar problems. Consider a project that deals with building a complex artifact like an advertising campaigns. Creating a campaign involves a lot of different communication channels between many people.</p>
<p style="text-align: justify;">On this project, the boss said:</p>

<blockquote>We should prevent the user from entering incorrect data.</blockquote>
<p style="text-align: justify;">Sure you don’t want to have corrupt data, which is reasonable: you don’t want to launch a campaign with wrong or corrupt data! However the users were telling a completely different story:</p>

<blockquote>[the process with all the strict validations] cannot be applied in practice, there’s no way it can work!</blockquote>
<p style="text-align: justify;">Why this conflict? In fact they are talking about two different processes, and they could not notice that. Sure, it takes the acute eyes of a Domain-Driven Design practitioner to recognize that subtlety!</p>
<p style="text-align: justify;">Alberto mentions what he calls the « <strong>Notorious Pub Hypothesis</strong>« : think about the pub where all the bad people gather at night, where you don’t go if you’re an honest citizen. The hypothesis comes from his mother asking:</p>
<p style="text-align: justify;"><img title="notorious_pub_hypothesis" src="http://cyrille.martraire.com/wp-content/uploads/2012/07/notorious_pub_hypothesis-300x176.png" alt="Why doesn't the police shut down this place?" width="300" height="176" /></p>
<p style="text-align: justify;">Why doesn’t the police shut down this place? Actually there is some value in having this kind of place, since the police knows where all the bad guys are, it makes it easier to find them when you have to.</p>
<p style="text-align: justify;">In a similar fashion, maybe there’s also a need somewhere for invalid data. What happens <strong>before</strong> we have strictly validated data?  Just like the bad guys who exist even if we don’t like it, there is a whole universe outside of the application, in which the users are preparing the advertising campaign with more than one month of preparation of data, lots of emails and many other communication types, and all that is untraceable so far.</p>

<address><em>Why not acknowledge that and include this process, a <em>collaborative</em> process, directly into the application?</em></address>
<h1 style="text-align: justify;">Similar data, totally different semantics</h1>
<p style="text-align: justify;">Coming from a data-driven mindset, it is not easy to realize that it’s not because the data structures are pretty much the same that you have to live with only one type of representation in your application. Same data, completely different behavior: this suggests different Bounded Contexts!</p>
<p style="text-align: justify;">The interesting pattern recurring in many applications is a split between<strong> two phases:</strong> one phase where multiple stakeholders collaborate on the construction of a deliverable, and a second phase where the built deliverable is stored, can be reviewed, versioned, searched etc.</p>
<p style="text-align: justify;">The natural focus of most projects seems to be on the second phase; Alberto introduced the name <strong>Collaborative Construction</strong> to refer to the first phase, often missed in the analysis. Now we have a name for this pattern!</p>
<p style="text-align: justify;">The insight in this story is to acknowledge the two contexts, one of collaborative construction, the other on managing the outcome of the construction.</p>

<h1 style="text-align: justify;">Looks like « source Vs. executable »</h1>
<p style="text-align: justify;">During collaborative construction, it’s important to accept <em>inconsistencies</em>, <em>warnings</em> or even <em>errors</em>, <em>incomplete</em> data, <em>missing</em> details, because the work is in progress, it’s a draft. Also this work in progress is by definition changing quickly thanks to the contributions of every participant.</p>
<p style="text-align: justify;">Once the draft is ready, it is then validated and becomes the final deliverable. This deliverable must be <em>complete</em>, <em>valid</em> and <em>consistent</em>, and cannot be changed any more. It is there forever. Every change becomes a new revision from now on.</p>
<p style="text-align: justify;">We therefore evolve from a <em>draft</em> semantics to a « printed » or « <em>signed</em> » semantics. The draft requires comments, conversations, proposals, decisions. On the other hand the resulting deliverable may require a version history and release notes.</p>
<p style="text-align: justify;">The insight that we have  these two different bounded contexts now in turn helps dig deeper the analysis, to discover that we probably need different data and different behaviors for each context.</p>
<p style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/07/collaborative_construction_examples.png"><img title="collaborative_construction_examples" src="http://cyrille.martraire.com/wp-content/uploads/2012/07/collaborative_construction_examples-300x133.png" alt="" width="300" height="133" /></a></p>
<p style="text-align: justify;">Some examples of this split in two contexts:</p>

<ul style="text-align: justify;">
	<li>The <strong>shopping cart</strong> is a work in progress, that once finalized becomes an <strong>order</strong></li>
	<li>A <strong>request for quote</strong> or an <strong>auction</strong> process is a collaborative construction in search of the best trade condition, and it finally concludes (or not) into a <strong>trade</strong></li>
	<li>A <strong>legal document draft</strong> is being worked on by many lawers, before it is signed off to become the legally binding <strong>contract</strong>, after the negotiations have happened.</li>
	<li>An example we all know very well, our <strong>source code</strong> in source control is a work in progress between several developers, and then the continuous integration compiles it into an <strong>executable</strong> and a set of reports, all immutable. It’s ok to have compilation errors and warnings while we’re typing code. It’s ok to have checkstyle violations until we commit. Once we release we want no warning and every test to pass. If we need to change something, we simply build another revision, each release cannot change (unless we patch but that’s another gory story)</li>
</ul>
<h1 style="text-align: justify;">UX demanding</h1>
<p style="text-align: justify;">Building software to deal with collaborative construction is quite demanding with respect to the <strong>User Experience</strong> (UX).</p>
<p style="text-align: justify;">Can we find examples of Collaborative Construction in software? Sure, think about <strong>Google Wave</strong> (though it did not end well), <strong>Github</strong> (successful but not ready for normal users that are not developers), <strong>Facebook</strong> (though we’re not building anything useful with it).</p>

<h1 style="text-align: justify;">Watch the video of the talk</h1>
<p style="text-align: justify;">Another note, among many other I took away from the talk, is that from time to time we developers should ask the question:</p>

<blockquote>what if the domain expert is wrong?</blockquote>
<p style="text-align: justify;">It does happen that the domain expert is going to mislead the team and the project, because he’s giving a different answer every day, or because she’s focusing on only one half of the full domain problem. Or because he’s evil…</p>

<div id="attachment_3469" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/alberto_ddd.png"><img title="alberto_ddd" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/alberto_ddd.png" alt="" width="271" height="193" /></a>Alberto in front of Campbell's Soup Cans, of course talking about Domain-Driven Design (picture Skillsmatter)</div>
<p style="text-align: justify;">And don’t hesitate to watch <a href="http://skillsmatter.com/podcast/design-architecture/why-do-all-my-ddd-applications-look-the-same" target="_blank">the 50mn video of the talk</a>, to hear many other lessons learnt, and also because it’s fun to listen to Alberto talking about zombies while talking about Domain-Driven Design!</p>
