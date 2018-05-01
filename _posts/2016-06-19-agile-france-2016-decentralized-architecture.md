---
layout: post
title: Agile France 2016 – Decentralized Architecture
date: 2016-06-19 16:06
author: admin
comments: true
categories: [Agilité, Evénements]
---
At Agile France 2016 in Paris I ran an open-space session on Decentralized Architecture. This was an opportunity to collect various perspectives on a topic that I’ve been thinking about and discussing with colleagues over the last few years.

<strong>Architecture Definition Ambiguity</strong>
We started with a quick survey with all the attendees, who were asked to give “examples of architecture”, in any form and in their own interpretation of the word.

Going through the examples, the very first realization was that in practice there are many different visions of architecture:

“Preventing accidental complexity”
“Ensuring consistency”, “avoiding doing the same thing twice”, “coordination between multiple applications”
“The norms &amp; blueprints: REST API, Service-Orientation…”
“The Main Decisions, especially the structuring decisions”
“The Vision, Consistency, Technical Debt management”
“The Coding guidelines: testable code, ability to extend…”
Psychological comfort: “We expect the architect to comfort the team and reassure them on the quality of their decisions”
Some of these definitions focus on the purpose, other on the solutions. We don’t mean the same thing at all when we use the word “architecture”. The definition of architecture is disturbingly ambiguous.

But this little survey was also a way for many to express their frustrations with respect to doing architecture and in particular with the architects.

<strong>The Architecture Frustration</strong>
<em>The bottleneck effect</em>
The most obvious complaint was that architecture as usually done by a central architecture group is a source of delays:

<em>“architects slow us down by several weeks on any new project”.</em>
As a result, it’s not uncommon for projects to game the rules to avoid going through the architects. For example if all projects beyond 100 man/day have to go through a mandatory architecture review, then a team with a genuine 120man/day project may try to split it into two 60 man/day projects to go under the threshold.

<strong>Conflicting schools of thoughts.</strong>
The other complaint is that “The architects impose arbitrary rules we disagree with, and that are detrimental to our project”.

Perhaps the team is just wrong, in which case the architects are trying to improve the things but they fail to convince. But perhaps the architects are outdated in their world view. In any case, when then teams and the architects follow different schools of thoughts then you can expect confusion, mis-communication and distrust.

<strong>Activity over Role</strong>
At this point of the session, another big realization was that we should not confuse the activity of “doing architecture” and the role of “the architect”. Taking care of the architecture is the most important, regardless of weather  you have a role of architect or not. This was the point put forward by my colleague Ly-Jia (@Ly_Jia) in her past talks on the topic.

<em>Architecture as an activity over Architect as a role</em>
Of course, when a team does not know how to architect (the activity), then they call an architect (the role) for help, who will bring their skills to solve the issues.

<strong>Architect as the Decision-Maker</strong>
While we mentioned before that some teams try to avoid dealing with their architects, other actually wish they had access to architects for advices or even to make the decisions.

<em>“We have no architect and we should have one to reduce our current mess”</em>

<em>“Without architect, it’s chaos.”</em>

<em>“We expect the architect to take the important decisions that are vital to the project”.</em>
In this view, the architect is the decision-maker for the team. The team is then supposed to follow the decisions. The architects should check the conformity after the work is done, even if they usually have no time to do that.

The architect as the decision-maker comes at a cost: it slows you down. This is hard to accept when you want short time-to-market. If you knew how to make decisions yourself in the team, you could go faster.

<strong>When the team knows how to do architecture</strong>
When the team has all the skills then it’s perfect, at least for their project. Decisions are local, fast, and well-informed.

<em>“We don’t have an architect and it’s fine”.</em>
This is an ideal situation we should nurture and encourage. However in practice not every team has all the skills. For example when surveying what’s important about architecture during the session at the conference, hardly anyone mentioned Data Authority as a concern.

Teams are probably not self-sufficient in all necessary skills. They probably need training and external help from people more skilled in architecture.

<strong>Architect as the Trainer on Architecture Skills</strong>
Many teams need training in architecture, and the architect is often the right person to teach that to the teams. In this view, the architect does not take decisions, but acts as a trainer, until there’s no need for training any more. It’s a biodegradable role, but with many teams, some turnover and the evolution of architecture skills, don’t expect that role to become useless anytime soon.

In fact this role of training the team already happens to some extent and informally during the meetings with the architects, even when the architects are the decision-makers. If they explain the decision-making and the rationale behind the decisions, then the attendees can learn how to reason like the architect. And perhaps next time the team can decide on its own in confidence.

<strong>Codifying the Architecture Skills</strong>
In a company I worked for, I used to collect every principle behind our decision-making in a list we called “the Codex”. This was an attempt to codify the way architects make decisions.

There were rules like “always know where’s the authoritative source of data”, or “don’t ever talk about solutions before you have stated the problem”, or “YAGNI: Don’t build a framework, build only what you need (it’s up to the next project to decide if they want to extract a framework from your stuff)”.
The main problem with the Codex was that it was quickly growing bigger and bigger. Last time I looked there were 30+ principles listed.

Even if everything can work fine locally thanks to skilled teams, perhaps the global consistency can still be endangered by the local ignorance of the bigger context.

Taking care of the architecture within the team can work at small scale. At larger scale, we need specific skills and more importantly we need specific visibility over many applications: this cross-system knowledge becomes the rare resource, only known by a few people. In bigger companies the central architects usually play this role of bringing this visibility to local teams to help them make informed decisions. But they remain bottlenecks on the path of the project delivery.

<strong>Bottleneck-free Architecture</strong>
Communities of Practices are a standard solution to give visibility on the overall state of the system to all teams. For example, delegates from each team meet in a 2-hours meetings every 2 weeks to exchange on the knowledge of the global architecture, the current stakes and challenges, what’s already existing somewhere else etc. It’s also a place to harmonize what they call architecture and what is desirable under this name.

In this view, the architecture can be seen as “the invisible frame, defined by consensus, which states what we like the system to be like”, as Jonathan (@jonathanperret) expressed it.

In addition to communities of Practices, tools can help too. For example GitHub Enterprise and its built-in search engine offers a simple way to explore the large scale galaxy of projects by searching for keywords. For example this is helpful to find out who’s also working on “Refund policies” in order to coordinate with them. And there is more we can do in this area to help make the bigger context accessible for a larger audience.
<strong>
Large-Scale Governance</strong>
At larger scale, governance becomes a topic that matters. We want to manage the legacy, decide which components we want to decommission and which components we would like to invest into to make them more useful.

At this level we often talk about a “master plan” (“schema directeur”). This is usually done by committee, and it can take a lot of time to define. This kind of master plan aims at optimizing the whole information system, its consistency, the data consistency, the investments in relation with the strategy, etc. This is again a bottleneck, and a large-scale one indeed.

If we want to avoid this bottleneck, then we want to favor local architects, embedded within teams or within small departments. They spend most of their time working with their colleagues, doing work and ideally wiring code as well. And they also meet regularly between peers to exchange and coordinate on company-wide blueprints, in a federated fashion.

Tools can help as well. Registry-like documentation, aka “applications catalogues” for example can enable anyone to have an overview of the overall system and their components.

However at large scale these documents are unlikely to be accessible to anyone but architects, as they are so complicated, with tons of boxes and arrows, or huge lists of recommendations, all with specific jargon.

Another problem at this scale is that the quantity of information to consider to reason on the architecture vastly exceeds the ability of any single brain. It’s just not possible for an architect, let alone for each team member, to be aware of every application, SLA and other concerns in a big company.

<strong>Coordination-less Architecture</strong>
Going a bit more radical at this point, is it really so obvious that this desirable “consistency” is always a good thing? Did anyone ever really investigated this question? Similarly, is waste necessarily a bad thing?

When we talk about all the expected benefits of architecture we easily see the value (remove duplication, standardize all the things, decide on THE unique tool for each function…) but we hardly see all the costs.

In particular most people ignore or underestimate the huge coordination cost associated with consensus on large-scale decisions. Most people also ignore or underestimate the cost of one-size-fits-all kind of decisions.

For example, contrast how standards are made by consortium, such as Corba or the various WS-* standards, with Internet standards, which require that for a standard to be chosen, there has to be at least two working implementations already available.

In the second approach we accept some waste (work being done more than once) for the sake of proving that a good approach is really a good one. In the first approach, we try to be perfect upfront, at the expense of the coordination cost of a long and expensive process. This is waste too, just a different kind of waste.

Once we accept some waste, we can embrace some diversity too. “Amazon now has one single deployment tool, but it eventually emerged from a diversity of 3 or 4 ones that used to be accepted for years”. It was ok to have more than one tool for a given functionality. In particular, the choice of the standard tool did not delays he delivery of any project.

Of course this doesn’t mean that diversity should be infinite either. “At Spotify they run meetings to limit the number of technologies currently in use”. But it’s an evolving thing. Anyone should be able to try a new technology for a while, which may or may not become part of the standard technology list later.

On the governance of the applications, which is also an evolving thing, we could learn to renew each part more often. We could put an expiration date on each component. Or limit their maximum size to ensure they can be re-written in a short period of time (microservices anyone?) This is in contrast with master plans that ambition to predict the future and try to precise the “target” of the information system. In fact I don’t know any sensible architect these days who still believes you could precise the target of the information system in details.

<strong>Emerging Architecture from Local Decisions</strong>
Going further, at large scale you don’t really have the choice on the way you do architecture. Consider Amazon which now has 1400 services: at this scale, you just can’t do architecture the old-fashion say, it’s impossible.

But you know that at Amazon each team lives by a limited number of strict rules that are absolutely mandatory: “2 pizza team”, “API-first” etc, as defined by Jeff Bezos in his famous memo. Few rules, strict, and promulgated by a dictator.

The challenge is to identify a set of dictatorial rules that do not constraint too much the freedom to deliver wonders, and that let the architecture emerge from purely local decisions.

It’s a challenge when you start a new company, and it’s another challenge when you already have a large legacy, both legacy systems and legacy culture. And nobody talks about the companies where the boss also decided of the rules and which failed.

<strong>Are Micro-Services about self-organizing teams?</strong>
The same morning at Agile France Emmanuel Gaillot (@egaillot) and Yannick Francois (@ya_f) had proposed a game on microservices where many teams had to build a game from many services, without any formal or central coordination. This was very interesting to experiment how many teams self-organize and how they find ways to avoid coordination as much as possible.

<strong>Ingredients for Emergence</strong>
How do we find out the few rules that everybody has to abide to and that enable emerging properties out of a large system? How do we deal with a number of parts we can’t keep into our heads?

We don’t have all the answers, but I’d mention 3 main orientations: Purpose, Options, and Automation.

<strong>Purpose-Oriented</strong>
A rule that will apply to anyone at any scale should be a direct expression of a company goal or a direct consequence of its vision. And if should be abstract enough to be interpreted differently in various contexts.

<strong>Option-Oriented</strong>
One key example of a general rule is “create (cheap) options for the future”. This one is so general that it could work pretty much everywhere. Perhaps that’s what Jeff had in mind with his API-First principle: “Whatever you build must give us the option to sell it to the outside world if we wish to”.

<strong>Conformity Automation</strong>
The third aspect is automation. If you really want fault-tolerance, then you should test for it. If you can’t do that by hand on a large system, robots can. This is what Netflix famously achieved with their Simian Army. They literally managed to automate a large part of checking their Architecture conformity with bots:

<em>Latency Monkey</em> induces artificial delays

<em>Conformity Monkey </em>finds instances that don’t adhere to best-practices and shuts them down.

<em>Doctor Monkey</em> taps into health checks to remove unhealthy instances.

<em>Janitor Monkey</em> searches for unused resources and disposes of them on the cloud.

<em>Security Monkey </em>finds security violations or vulnerabilities and terminates the offending instances.

<em>10-18 Monkey</em> detects configuration and run time problems in instances serving customers in multiple geographic regions.

<em>Chaos Gorilla</em> is similar to Chaos Monkey, but simulates an outage of an entire Amazon availability zone.
This automation forces everyone in each team to own the concerns enforced by the Simian Army, which also happen to be direct requirements. That’s a good thing. We’re not that far from a genuine Test-Driven Architecture at this stage.

<strong>Closing</strong>
Thanks everyone for the discussions in this session!
