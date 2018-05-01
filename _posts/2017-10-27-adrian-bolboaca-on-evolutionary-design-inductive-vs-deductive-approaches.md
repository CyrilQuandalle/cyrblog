---
layout: post
title: "Adrian Bolboaca on Evolutionary Design: Inductive vs. Deductive approaches"
date: 2017-10-27 17:30
author: cyrille martraire
comments: true
categories: [Bonnes pratiques de dév, DDD, design, Evolutionary, Programmation, refactoring, TDD]
---
I’ve been lucky to attend a very interesting meetup with <a href="https://twitter.com/adibolb">Adrian Bolboaca</a> recently at <a href="https://twitter.com/arollafr">Arolla</a> on the edgy topic of Evolutionary Design using Inductive or Deductive approach. It is about TDD of course, as Adi is a master of TDD. The topic could not escape my mind since then, so I’d like to share about it here, even if it remains not fully baked. I also hope Adi will share his material on all this soon.

<blockquote>Evolutionary Design is the art of growing a system by observing its natural traits then normalizing and optimizing its growth. — Adrian Bolboaca</blockquote>

In Adi words, normalizing means refactoring or any other transformation on the code, like extracting a Bounded Context out of a bigger piece of code.

The opportunity for transformations is when we add new behaviours or tests. Therefore,

<blockquote>The important thing is which behaviour to add and when.</blockquote>

Going too fast would be “like putting too much chemicals on plants and they die.”

<h1>Inductive approach of Evolutionary Design</h1>

Inductive reasoning is the derivation of general principles from specific observations. It’s about finding broad generalizations from concrete examples.

<h2>Behaviour Slicing from simple to complex</h2>

Behaviour Slicing in an inductive approach is a way to define the tests progression that we will follow to drive our Test-Driven Development. The technique is shown below on the flip chart, illustrated on a simple addition function:

&nbsp;

https://twitter.com/cyriux/status/923247849609093120
Typically, we would start with a focus on the happy path cases before we would move to the negative path cases.

We then progress in Baby Steps. This includes Baby Steps in design, which Adi defines as:

<blockquote>Baby Steps for design is introducing only one concept at a time</blockquote>

Putting this in practice, we find out that the rule “Introducing only one concept at a time” in the tic-tac toe kata suggests the missing case of “empty board, nobody wins” that we haven’t noticed so far.

And if you can’t find a case without introducing more than one concept, then simplify the problem (even artificially), e.g. Reduce the 3x3 board to a 1x1 board to introduce just Token but not Position yet.

The goal in this inductive approach is to evolve from primitives to [clusters of] design concepts. We progress from generalizing small behaviour into design concepts (…) which we then generalize into design concepts, (…) which we then fragment into modules and Bounded Contexts. As commonly known in TDD circles, the trigger for a transformation is typically the “Rule of 3", which Adi explains:

<blockquote>We use the “Rule of 3” as a prerequisite for refactoring to avoid generalizing from accidental coincidences. And the number 3 is only indicative, sometime you need to go to up to 7 occurrences to be sure.</blockquote>

In this progression, the behaviours matter, not the implementation. We could re-implement later and the tests should still pass.

As in classical <a href="https://cumulative-hypotheses.org/2011/08/30/tdd-as-if-you-meant-it/">TDD As If You Meant It</a> approach, from <a href="https://twitter.com/keithb_b">Keith Braithwaite</a>, we follow a strict progression for introducing new design concepts out of the pre-existing one, going from Scalars to primitives, Field or Parameter, to Settings class, to Data Structure, Logic to Pure Function, Class Function, to Component:

<img class="progressiveMedia-image js-progressiveMedia-image" src="https://cdn-images-1.medium.com/max/800/1*QAr-ns2vSwRBl-uCC4yJFw.jpeg" data-src="https://cdn-images-1.medium.com/max/800/1*QAr-ns2vSwRBl-uCC4yJFw.jpeg" />

As a consequence,

<blockquote>following a pure inductive approach is a “very functional approach: You can only create a class from one or more pure functions you have already.</blockquote>

Note that this is different from designing your failing test from your dream API (“Assuming it was done and perfect for me as a user, how would it look like?”); in contrast, here we even let the functions and their signature emerge from the behaviour underneath.

This inductive approach overall is closer to the “Bottom up”, “classicist” or “Chicago school” styles of TDD.

<h1>Deductive approach of Evolutionary Design</h1>

The deductive approach involves “More design upfront”, and the focus is more on collaboration (interaction) behaviour. From Wikipedia, Deductive reasoning is the process of reasoning from premises to reach a logically certain conclusion. Here it is not about generalizing from examples, but about starting from what is given (e.g. the behaviour expected by the user) and then propagate the consequences to the design.

We start by creating a Scaffolding, which can be a Guiding Test (GOOS), a Bounded Context with business entities (DDD), or a Walking skeleton (Cockburn). Then we deduce the design concepts depending on the scaffolding.

<blockquote>We find an entry point: typically what the user sees. Then we find the next layer of collaboration. We iteratively understand if we reach the end, for example if our guiding test is green, or when we reach the storage with a walking skeleton, and then we refactor the design concepts.</blockquote>

A big difference with the inductive approach is that:

<blockquote>the tests are generic from the beginning, they start by using design concepts early on, and the underlying representation needs to be addressed minimally.</blockquote>

The essentials of a deductive approach are the links between the design concepts, through their API and collaborators, as they would show in a dependency graph.

One drawback of a deductive approach is that:

<blockquote>it’s harder to test, and the tests make refactoring more difficult.</blockquote>

<h2>Behaviour Slicing, deductive style</h2>

Behaviour Slicing deductive style is different than in inductive style. We would inventory all the entities we have in mind, e.g. for the Tic-tac toe we would list <em>Board, Game, Turn, Game Result, Position, Player, Token.</em>

The Inputs and Outputs chart would include cases like: <em>When </em>game<em> starts | Validate that the game has 3 players, and a board, which is empty.</em>

From this coarse grain upfront design we can then implement from the outside, starting with mocks first, as in a <a href="https://codurance.com/2017/10/23/outside-in-design/">mockist</a> style.

After almost 3 hours of talking and live-coding, Adi concluded by inviting us at considering Tests as pressure, and from that:

<blockquote>We observe growth patterns that simplify the resulting system.</blockquote>

Adi also listed some anti-patterns, like considering that you know the resulting design in advance, and not listening to the design smells.

<h1>My takeaway</h1>

A lot of food for thoughts indeed:

https://twitter.com/ArollaFr/status/923278040179802118

A lot of food for thoughts, digging deep into the essence of evolutionary design through TDD.

“<em>Inductive vs Deductive</em>” is not fully equivalent to the “<em>Mockist vs Classicist</em>” dichotomy (or debate). It is more essential than that.

Inductive starts from the minimal description of the behaviour, introducing concepts when proven necessary. This means we start with primitives and built-in basic language constructs <em>before</em> we extract higher level concepts. This fits well for everything algorithm-ish.

Deductive is Contract-Driven, and fits better the edges of a system:

<ul>
    <li>Starting from you dream API from a Developer Experience point of view.</li>
    <li>Starting from given JSON contracts</li>
    <li>Starting from the domain language, the Ubiquitous Language</li>
</ul>

Note that in all three examples, you can probably implement in a deductive approach in a full classicist style, without mocks.

Also, in this view, conforming to the Ubiquitous Language is a kind of contract, as it’s a given and we try very hard to only name domain objects after the domain language.

When contracts are given, we have to follow them, so we are forced using a deductive approach. This typically happens on the edges of a system, with Web-Services and integration to the infrastructures.

When there’s no given contract, we prefer to let things emerge out of necessity. But we may also decide to introduce intermediary contracts for our own design reasons, e.g. to split the work between teams, or to limit each module to a manageable (small) size.

However we may negotiate and evolve contracts, and we would for sure evolve the Ubiquitous Language in a mix of deductive (using the words from the problem statement) and inductive (driven by necessity from refactoring the behaviour). Most contracts evolve from the implementation feedback: “<em>there’s a concept of CalculationPreferences missing in the contract</em>”, or “<em>we got that concept of End Date wrong in the contract, the date should not be a date but a number of days after this other date.</em>”

<h2>Mixing inductive and deductive approaches</h2>

I tend to think that we usually we have a mix of both deductive constraints and inductive freedom.

Every system has edges where it interacts with external actors. For the system to be valuable, it’s good practice to start with the best contracts from the client (external actor getting value from our system) perspective: the ideal API, or the ideal REST interaction.

Note that for providers it can be the other way round: define our ideal contracts from the internal needs of the system.

When we decide to follow a Domain-Driven approach, we explicitely decide that we want the domain model to conform to the Ubiquitous Language of the business. It’s a decision to be <em>Conformist</em>, in the DDD Context Mapping sense. Therefore we are constrained by the language (don’t worry, it’s an enabling constraint) but we have a lot of freedom to induce lower-level implementation concepts, and even to suggest changes to the Ubiquitous Language.

All this also suggests we are getting increasingly closer to really understanding how design works. Could this help automate design one day?
