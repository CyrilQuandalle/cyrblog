---
layout: post
title: Canary tests
date: 2014-02-04 10:37
author: cyrille martraire
comments: true
categories: [canary, Continuous Delivery, devops, environment, infrastructure, living documentation, Outils, Programmation, test]
---
<p style="text-align: justify;"><strong>Canary Tests</strong> are minimal tests to quickly and automatically verify that everything you depend on is ready. You run Canary tests before other time-consuming tests, and before wasting time investigating your code when the other tests are red. If the canary test fails, you know you have to fix something on the environments first.</p>

<address>This idea of Canary test is different from the Canary Deployment. In Canary Deployment you deploy to a small fraction of your users to check everything’s fine before rolling out to more users.</address>

<address><a href="http://cyrille.martraire.com/wp-content/uploads/2014/01/20140120-2358251.jpg"><img title="20140120-235825.jpg" alt="" src="http://cyrille.martraire.com/wp-content/uploads/2014/01/20140120-2358251-300x264.jpg" width="300" height="264" /></a></address>

<h1 style="text-align: justify;">Save time by checking what should be always OK</h1>

<p style="text-align: justify;">Canary tests check for the obvious and frequent sources of issues, such as:</p>

<ul style="text-align: justify;">
    <li><strong>connectivity to network</strong>: firewall rules ok, ports open, proxy working fine, NAT, ping below a good threshold</li>
    <li><strong>databases and middleware are up</strong></li>
    <li><strong>disk quota</strong> for logs not almost full</li>
    <li>every needed <strong>login </strong>and <strong>password </strong>is valid</li>
    <li><strong>installed software</strong> available in the right version: dll installed, registry set-up, environment variables set, user directories all exist, the frameworks and OS versions are fit, timezone and locale are as expected</li>
    <li><strong>reference data integrity</strong> and consistency (dates, valuations…) are ok</li>
    <li><strong>database schema and audit of applied scripts </strong>are as expected</li>
    <li><strong>licences are not expired</strong> (there is usually a way to check that automatically)</li>
</ul>

<p style="text-align: justify;">Canary tests should run regularly, ideally before any expensive tests like end-to-end tests. Of course you want to run them whenever there is a trouble somewhere, before wasting time on manual investigations in your code when the expected environment is not fully available.</p>

<p style="text-align: justify;">Even at the code level, a canary test is just a trivial test to verify that the testing framework works correctly, as mentioned by <a href="http://twitter.com/grenangen">Marcus</a> on his blog:</p>

<pre>   assertTrue(true)</pre>

<p style="text-align: justify;">Don’t forget to verify that your tests can fail too!</p>

<h1 style="text-align: justify;">Simple and low-maintenance</h1>

<p style="text-align: justify;">The canary test tools should not assume much from the application. They must be independent from new developments to be as stable as possible. They should require little to no maintenance at all.</p>

<p style="text-align: justify;">One way to do that in practice is to simply scan configuration files for every URL, password and just ping them one by one against a predefined time threshold. Any log path mentioned in the configuration files can be scanned and checked for the required write permissions and available disk space. Any login and password can be checked, even though this may be more complicated.</p>

<h1 style="text-align: justify;">Canary tests are documentation too</h1>

<p style="text-align: justify;">Doing Canary tests may require explicit declarations of expectations, e.g. an annotation AssumedPermission(’777′) to declare the permissions required on the files referenced in the configuration files. Alternatively you may rely on a Convention Over Configuration principle. For example every</p>

<pre> log.*.path</pre>

<p style="text-align: justify;">variable is assumed  to be a log path to check against some predefined expectations like being writable and being ok with disk quota.</p>

<p style="text-align: justify;">When you add canary tests, this automation itself is a form of documentation that makes assumption more explicit.</p>

<p style="text-align: justify;">You could export a report of every canary test that has been ran into a readable form that can become part of your <em>Living Documentation</em>.</p>
