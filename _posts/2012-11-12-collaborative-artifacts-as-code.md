---
layout: post
title: Collaborative Artifacts as Code
date: 2012-11-12 17:42
author: cyrille martraire
comments: true
categories: [Alberto Brandolini, artifact, bdd, Bonnes pratiques de dév, collaboration, devops, diagram, documentation, impact mapping, infrastructure as code, markdown, module, plain text, Programmation, project, readme, scm, software factory]
---
<p style="text-align: justify;">A software development project is a collaborative endeavor. Several team members work together and produce artifacts that evolve continuously over time, a process that Alberto Brandolini (<a href="https://twitter.com/ziobrando">@ziobrando</a>) calls <a title="Collaborative Construction by Alberto Brandolini" href="http://www.arolla.fr/blog/2012/10/collaborative-construction-by-alberto-brandolini/" target="_blank">Collaborative Construction</a>. Regularly, these artifacts are taken in their current state and transformed into something that become a release. Typically, source code is compiled and packaged into some executable.</p>
<p style="text-align: justify;">The idea of Collaborative Artifacts as Code is to acknowledge this collaborative construction phase and push it one step further, by promoting as many collaborative artifacts as possible into plain text files stored in the same source control, while everything else is generated, rendered and archived by the software factory.</p>
<p style="text-align: justify;">Collaborative artifacts are the artifacts the team works on and maintains over time thanks to the changes done by several people through a source control management such as SVN, TFS or Git, with all their benefits like branching and versioning.</p>

<h1 style="text-align: justify;">Keep together what varies together</h1>
<p style="text-align: justify;">The usual way of storing documentation is to put MS Office documents into a shared drive somewhere, or to write random stuff in a wiki that is hardly organized.</p>
<p style="text-align: justify;">Either way, this documentation will quickly get out of sync because the code is continuously changing, independently of the documents stored somewhere else, and as you know, “Out of sight, out of mind”.</p>

<div id="attachment_1154" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2007/10/best-bestof-43.jpg"><img title="best-bestof-43" src="http://cyrille.martraire.com/wp-content/uploads/2007/10/best-bestof-43.jpg" alt="" width="360" height="480" /></a>we now have better alternatives</div>
<h1 style="text-align: justify;">We now have better alternatives</h1>
<p style="text-align: justify;">Over the last few years, there has been changes in software development. <a href="https://github.com/" target="_blank">Github </a>has popularized the README.md overview file written in <a href="http://www.codinghorror.com/blog/2012/10/the-future-of-markdown.html" target="_blank">Markdown</a>. DevOps brought the principle of <a href="http://stochasticresonance.wordpress.com/2009/07/12/infrastructure-renaissance/">Infrastructure as Code</a>. The <a href="http://dannorth.net/introducing-bdd/" target="_blank">BDD </a>approach introduced the idea of text scenarios as a living documentation and an alternative for both specifications and acceptance tests. New ways of planning what a piece of software is supposed to be doing have appeared as in <a href="http://impactmapping.org/">Impact Mapping</a>.</p>
<p style="text-align: justify;">All this suggests that we could replace many informal documents by their more structured alternatives, and we could have all these files collocated within the source control together with the source.</p>
<p style="text-align: justify;">In any given branch in the source control we would then have something like this:</p>

<ul style="text-align: justify;">
	<li><strong>Source code</strong> (C#, Java, VB.Net, VB, C++)</li>
	<li><strong>Basic documentation</strong> through plain README.md and perhaps other .md files wherever useful to give a high-level overview on the code</li>
	<li><strong>SQL code</strong> as source code too, or through Liquibase-style configuration</li>
	<li><strong>Living Documentation</strong>: unit tests and BDD scenarios (SpecFlow/Cucumber/JBehave feature files) as living documentation</li>
	<li><strong>Impact Maps</strong> (and every other mindmaps), may be done as text then rendered via tools like text2mindmap</li>
	<li><strong>Any other kind of diagrams</strong> (UML or general purpose graphs) ideally be defined in plain text format, then rendered through tools (Graphviz, yUml).</li>
	<li><strong>Dependencies declarations</strong> as manifest instead of documentation on how to setup and build manually (Maven, Nuget…)</li>
	<li><strong>Deployment code</strong> as scripts or Puppet manifests for automated deployment instead of documentation on how to deploy manually</li>
</ul>
<h1 style="text-align: justify;">Plain Text Obsession is a good thing!</h1>
<p style="text-align: justify;">Nobody creates software by editing directly the executable binary that the users actually run at the end, yet it is common to directly edit the MS Word document that will be shipped in a release.</p>
<p style="text-align: justify;">Collaborative Artifacts as Code suggests that every collaborative artifact should be text-based to work nicely with source control, and to be easy to compare and merge between versions.</p>
<p style="text-align: justify;">Text-based formats shall be preferred whenever possible, e.g. .csv over xls, rtf or .html over .doc, otherwise the usual big PPT files must go to another dedicated wiki where they can be safely forgotten and become instantly deprecated…</p>

<h1 style="text-align: justify;">Like a wiki, but generated and read-only</h1>
<p style="text-align: justify;">My colleague <a href="http://tpierrain.blogspot.fr/" target="_blank">Thomas Pierrain</a> summed up the benefits of this approach, for a documentation:</p>

<ul style="text-align: justify;">
	<li>always be <strong>up-to-date</strong> and <strong>versioned</strong></li>
	<li>easily <strong>diff-able</strong> (text filesn e.g. with Markdown format)</li>
	<li>respect the <strong>DRY</strong> principle (with the SCM as its golden source)</li>
	<li>easily <strong>browsable</strong> by everyone (DEV, QA, BA, Support teams…) in the readonly and readable wiki-like web site</li>
	<li>easily modifiable by team members in a <strong>well know and official location</strong> (as easy as creating or modifying a text file in a SCM)</li>
</ul>
<h1 style="text-align: justify;">What’s next?</h1>
<p style="text-align: justify;">This approach is nothing really new (think about LateX…), and many of the tools we need for it already exist (Markdown renderers, web site to organize and display Gherkin scenarios…). However I have never seen this approach fully done in an actual project. Maybe your project is already doing that? please share your feedback!</p>
