---
layout: post
title: Surface-area over volume ratio – a metaphor for software design
date: 2012-11-19 10:27
author: cyrille martraire
comments: true
categories: [area, capability, cheaper, design, duck typing, economics, facet, interfaces, metaphor, multi-dimension, physics of software, Programmation, protocols, quality, surface, volume]
---
<p style="text-align: justify;">There’s a metaphor I had in mind for a long time when thinking about software design: because I’m proudly lazy, in order to make the code smaller and easier to learn, I must do my best to reduce the « surface-area over volume ratio » of the software.</p>

<h1 style="text-align: justify;">Surface-area over volume ratio?</h1>
<p style="text-align: justify;">I like the <a href="http://en.wikipedia.org/wiki/Surface-area-to-volume_ratio" target="_blank">Surface-area over volume ratio</a> as a metaphor to express how to make software cheaper to discover and learn, and smaller to maintain as well.</p>
<p style="text-align: justify;">For a given object, the surface-area over volume ratio is the amount of surface area per unit volume. For buildings and for animals, the smaller this ratio, the less the heat loss during the winter, hence a better thermal efficiency.</p>
<p style="text-align: justify;">Have you ever noticed that huge warehouses were always cool even during the summer when it’s hot? This is just because in our real 3D world the surface-area over volume ratio is much smaller when the absolute size of the building increases.</p>
<p style="text-align: justify;">The theory also mentions that the sphere is the optimal shape with respect to this ratio. In fact, the more « compact » the less the ratio, or the other way round we could define compactness of an object directly by its surface-area-over-volume ratio.</p>

<div id="attachment_3490" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/Dodecahedron1.png"><img title="Dodecahedron" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/Dodecahedron1.png" alt="" width="300" height="300" /></a>A dodecahedron, a volume that approximates a sphere with just 2D facets (Wikipedia picture)

</div>
<h1 style="text-align: justify;">What about software design?</h1>
<p style="text-align: justify;">Let’s consider that each <strong>method signature of each interface</strong> is part of the <strong>surface-area</strong> of the software, because this is what I have to learn primarily when I join the project. The larger the surface-area, the more time I’ll need to learn, provided I can even remember all of it.</p>

<blockquote>Larger surface is not good for the developers.</blockquote>
<p style="text-align: justify;">On the  other hand, the <strong>implementation</strong> is part of what I would call the <strong>volume</strong> of the software, i.e. this is where the code is really doing its stuff. The more volume, the more powerful and richer the software. And of course the point of Object Orientation is that you don’t have to learn all the implementation in order to work on the project, as opposed to the interfaces and their method signatures.</p>

<blockquote>Larger volume is good for the users (or the value brought by the software in general)</blockquote>
<p style="text-align: justify;">As a consequence we should <strong>try to minimize the surface-area over volume ratio</strong>, just like we’re trying to reduce it when designing a green building.</p>
<p style="text-align: justify;">Can we extrapolate that we should design software to be more « compact » and more « sphere »-like?</p>

<h1 style="text-align: justify;">Facets-like interfaces</h1>
<p style="text-align: justify;">Reusing the same interface as much as possible is obviously a way to reduce the surface-area of the software. Adhering to interfaces from the JDK or Google Guava, interfaces that are already well-known, helps even better: in our metaphor, an interface that we don’t have to learn comes for free, like a perfectly isolated wall in a building. We can almost omit it in our ratio.</p>
<p style="text-align: justify;">To further reduce the ratio, we can find out every opportunity to use as much as possible the minimum set of common interfaces, even over unrelated concepts. At the extreme of this approach we get <a href="http://en.wikipedia.org/wiki/Duck_typing" target="_blank">duck typing</a> in dynamic languages. In our usual languages like Java or C# we must introduce additional small interfaces, usually with one single method.</p>
<p style="text-align: justify;">For example in a trading system, every class with a <em>isInCurrency(Currency)</em> method can implement a common interface <em>CurrencySpecific</em>. As a result, a lot of processing (filtering etc.) on <em>stuff that is related to currencies in some</em> way can be done on all these classes without any knowledge about them, except their currency-specificity.</p>
<p style="text-align: justify;">In this example, the currency-specificity we extracted into one interface is like a single facet over a larger volume made of several implementation. It makes our design more compact, it will be easier to learn, while offering a rich set of behaviors.</p>
<p style="text-align: justify;">The limit for this approach of putting a lot of implementation code under the same interface is that sometimes it really makes no domain sense. Since code is primarily meant to describe the domain, without causing confusion we must be careful not to go too far. We must also take great care when sharing interfaces between bounded contexts, there’s a high risk of excessive coupling.</p>

<div id="attachment_3489" style="text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/faceted-artwork.jpeg"><img title="faceted-artwork" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/faceted-artwork-300x224.jpg" alt="" width="300" height="224" /></a>Faceted artwork (picture from http://reinierdejong.wordpress.com)

</div>
<h1 style="text-align: justify;">Yet another metric?</h1>
<p style="text-align: justify;">This metric could be measured by a tool, however the primary value is not in checking the figures, but in the thinking and taking care of making the design easy to learn (less surface-area), while delivering a lot of valuable behaviors (more volume).</p>
