---
layout: post
title: "TDD Vs. math formalism: friend or foe?"
date: 2013-01-07 11:37
author: cyrille martraire
comments: true
categories: [Bonnes pratiques de dév, cycle, DDD, design, domain, enum, established, finance, formalism, group, IMM, integers, maths, ordering, partial order, Programmation, supple, TDD]
---
<p style="text-align: justify;">It is not uncommon to oppose the empirical process of TDD, together with its heavy use of unit tests, to the more mathematically based techniques, with the  "formal methods" and formal verification at the other end of the spectrum. However I experienced again recently that the process of TDD can indeed help discover and draw upon math formalisms well-suited to the problem considered. We then benefit from the math formalism for an easier implementation and correctness.</p>
<p style="text-align: justify;">It is quite frequent that maths structures, or more generally « established formalisms » as Eric Evans would say, are hidden everywhere in the business concepts we need to model in software.</p>
<p style="text-align: justify;">Dates and how we take liberties with them for trading of financial instruments offer a good example of a business concept with an underlying math structure: traders of futures often use a notation like ‘U8′ to describe an expiry date like September 2018; ‘U’ means September, and the ’8′ digit refers to 2018, but also to 2028, and 2038 etc. Notice that this notation only works for 10 years, and each code is recycled every decade.</p>

<div id="attachment_3741" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2013/01/IMM-pit.jpg"><img title="IMM-pit" src="http://cyrille.martraire.com/wp-content/uploads/2013/01/IMM-pit-300x168.jpg" alt="" width="300" height="168" /></a>The IMM trading floor in the early 70's (photo CME Group)</div>
<p style="text-align: justify;">In the case of IMM contract codes, we only care about quarterly dates on:</p>

<ul style="text-align: justify;">
	<li>March (H)</li>
	<li>June (M)</li>
	<li>September (U)</li>
	<li>December (Z)</li>
</ul>
<p style="text-align: justify;">This yields only 4 possibilities for the month, combined with the 10 possible year digits, hence 40 different codes in total, over the range of 10 years.</p>

<h2 style="text-align: justify;">How does that translate into source code?</h2>
<p style="text-align: justify;">As a software developer we are asked all the time to manage such IMM expiry codes:</p>

<ul style="text-align: justify;">
	<li><strong>Sort</strong> a given set of IMM contract codes</li>
	<li>Find the <strong>next</strong> contract from the current « leading month » contract</li>
	<li>Enumerate the <strong>next 11</strong> codes from the current « leading month » contract, etc.</li>
</ul>
<p style="text-align: justify;">This is often done ad hoc with a gazillion of functions for each use-case, leading to thousands of lines of code hard to maintain because they involve parsing of the ‘U8′ format everytime we want to calculate something.</p>
<p style="text-align: justify;">With TDD, we can now tackle this topic with more rigor, starting with tests to define what we want to achieve.</p>
<p style="text-align: justify;">The funny thing is that in the process of doing TDD, the cyclic logic of the IMM codes struck me and strongly reminded me of the cyclic group Z/nZ. I had met this strange maths creature at school many years ago, I had a hard time with it by the way. But now on a real example it was definitely more interesting!</p>

<address><a href="https://github.com/cyriux/RealWorldAlgebra" target="_blank">The source code (Java) for this post is on Github</a>.</address>
<h2 style="text-align: justify;">Draw on established formalisms</h2>
<p style="text-align: justify;">Thanks to Google it is easy to find something even with just a vague idea of how it’s named, and thanks to Wikipedia, it is easy to find out more about any established formalism like <a href="http://en.wikipedia.org/wiki/Cyclic_group">Cyclic Groups</a>. In particular we find that:</p>

<blockquote>Every finite cyclic group is isomorphic to the group { [0], [1], [2], …, [n − 1] } of integers modulo n under addition</blockquote>
<p style="text-align: justify;">The Wikipedia page also mentions a concept of the product of cyclic groups in relation with their order (here the number of elements). Looks like this is the math-ish way to say that 4 possibilities for quarterly months combined with 10 possible year digits give 40 different codes in total.</p>
<p style="text-align: justify;">So what? Sounds like we could identity the set of the 4 months to a cyclic group, the set of the 10 year digits to another, and that even the combination (product) of both also looks like a cyclic group of order 10 * 4 = 40 (even though the addition operation will not be called like that). So what?</p>
<p style="text-align: justify;">Because we’ve just seen that there is an isomorphism between any finite cyclic group and the cyclic group of integer of the same order, we can just switch to the integer cyclic group logic (plain integers and the modulo operator) to simplify the implementation big time.</p>
<p style="text-align: justify;">Basically the idea is to convert from the IMM code « Z3″ to the corresponding ‘ordinal’ integer in the range 0..39, then do every operation on this ‘ordinal’ integer instead of the actual code. Then we can format back to a code « Z3″ whenever we really need it.</p>

<h2 style="text-align: justify;">Do I still need TDD when I have a complete formal solution?</h2>
<p style="text-align: justify;">I must insist that I did not came to this conclusion as easily. The process of TDD was indeed very helpful not to get lost in every possible direction along the way. Even when you have found a formal structure that could solve your problem in one go, even in a « formal proof-ish fashion », then perhaps you need less tests to verify the correctness, but you sure still need tests to think on the specification part of your problem. This is your gentle reminded that TDD is not about unit tests.</p>

<h2 style="text-align: justify;">Partial order in a cyclic group</h2>
<p style="text-align: justify;">Given a list of IMM codes we often need to sort them for display. The problem is that a cyclic group has no total order, the ordering depends on where you are in time.</p>
<p style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2013/01/cycle_of_week_days.png"><img title="cycle_of_week_days" src="http://cyrille.martraire.com/wp-content/uploads/2013/01/cycle_of_week_days-300x158.png" alt="" width="300" height="158" /></a></p>
<p style="text-align: justify;">Let’s take the example of the days of the week that also forms a cycle: MONDAY, TUESDAY, WEDNESDAY…SUNDAY, MONDAY etc.</p>
<p style="text-align: justify;">If we only care about the future, is MONDAY before WEDNESDAY? Yes, except if we’re on TUESDAY. If we’re on TUESDAY, MONDAY means next MONDAY hence comes after WEDNESDAY, not before.</p>
<p style="text-align: justify;">This is why we cannot unfortunately just implement Comparable to take care of the ordering. Because we need to consider a reference IMM code-aware partial order, we need to resort to a Comparator that takes the reference IMM code in its constructor.</p>
<p style="text-align: justify;">Once we identify that situation to the cyclic group of integers, it becomes easy to shift both operands of the comparison to 0 before comparing them in a safe (total order-ish) way. Again, this trick is made possible by the freedom to experiment given by the TDD tests. As long as we’re still green, we can go ahead and try any funky approach.</p>

<h2 style="text-align: justify;">Try it as a kata</h2>
<p style="text-align: justify;">This example is also a good coding kata that we’ve tried at work not long ago. Given a simple presentation of the format of an IMM contract code, you can choose to code the sort, find the next and previous code, and perhaps even optimize for memory (cache the instances, e.g. lazily) and speed (cache the toString() value, e.g. in the constructor) if you still have some time.</p>

<h2 style="text-align: justify;">In closing</h2>
<p style="text-align: justify;">Maths structures are hidden behind many common business concepts. I developed an habit to look for them whenever I can, because they always help make us think, they help question our understanding of the domain problem (« is my domain problem really similar in some way to this structure? »), and of course because they often offer wonderful ready-made implementation hints!</p>

<address><a href="https://github.com/cyriux/RealWorldAlgebra" target="_blank">The source code (Java) for this post is on Github</a>.</address>
