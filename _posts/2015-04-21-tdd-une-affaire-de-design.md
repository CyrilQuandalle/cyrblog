---
layout: post
title: TDD, une affaire de design
date: 2015-04-21 17:24
author: cyrille martraire
comments: true
categories: [Bonnes pratiques de dév, DDD, design, Programmation, TDD, training]
---
Le sujet de la semaine, c'est une <a href="https://twitter.com/sandromancuso/status/588503877235781632" target="_blank">longue conversation sur Twitter</a> entre Sandro Mancuso (<a href="https://twitter.com/sandromancuso" target="_blank">@sandromancuso</a>), Ron Jeffries (<a href="https://twitter.com/RonJeffries" target="_blank">@ronjeffries</a>), et Joe Rainsberger (<a href="https://twitter.com/jbrains" target="_blank">@jbrains</a>) et quelques autres sur les relations entre TDD et le Design. On rappelle que TDD est habituellement présenté comme une technique de design.

Sandro a commencé par affirmer que TDD avait besoin de compétences en design au préalable.

<blockquote>I believe software design should be taught before TDD. TDD can’t lead to good design if we don’t know what good design looks like. - @sandromancuso</blockquote>

Joe et Ron ont challengé cette affirmation, mais davantage sur la forme que sur le fond.

<h1>Faut-il apprendre les techniques de design pour faire du TDD ?</h1>

Au fil de cette conversation il apparaît clairement un contraste entre la vision de TDD des pionniers à la fin des années 90, et la vision des nouveaux praticiens TDD de la fin des années 2000.

Pour les pionniers, l'industrie logicielle à la fin des années 90 avait un focus marqué sur le design. Les techniques de design orientées-objet étaient nombreuses, certaines compliquées ou parfois exotiques. On se passionnait pour les patterns, bien au-delà des 23 design patterns. On débattait de cohésion et de couplage. Oncle Bob publiait sur les principes SOLID. On parlait de la loi de Demeter, de rôles et de responsabilités, de techniques d'analyse comme les CRC Cards.

On faisait aussi du design avant de coder (Big Design Upfront, BDUF). Et quand on parlait de design, UML n'était pas loin. Pire encore, le design était considéré noble tandis que coder était parfois considéré sale, ou au mieux automatisable, avec des démarches telles que MDA.

En tout cas pour être crédible à cette période il fallait s'y connaître en design. C'était donc le cas de la plupart des développeurs qui font autorité aujourd'hui en XP, Agile et TDD.

Le manifeste agile est arrivé un peu en réaction aux excès de cette période, avec TDD au premier rang. Au-delà du rejet des aberrations en gestion des projets de développement logiciel, on rejette aussi les excès du design pour le design.

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/04/tdd-red-refactor-green.jpg" ><img class="aligncenter wp-image-3208" src="http://www.arolla.fr/blog/wp-content/uploads/2015/04/tdd-red-refactor-green-300x131.jpg" alt="tdd-red-refactor-green" width="500" height="219" /></a>

Arrêtez de spéculer sur les besoins futurs, privilégiez la simplicité et l'humilité. Commencez toujours par des tests, par petits pas. Refactorez, tout le temps !

<h1>Histoire d'un malentendu</h1>

<blockquote>I have the feeling that our industry is going from BDUF to “no design at all”, only relying on TDD with no foundation. - @sandromancuso in reply to @jbrains</blockquote>

Depuis si on écoute les partisans de TDD, dont je fais partie, on peut légitimement avoir l'impression qu'avec TDD le design émerge spontanément, "mécaniquement", et même pourquoi pas "forcément" de l'application rigoureuse du cycle Test-Code-Refactor de TDD. Ce n'est pas malheureusement pas du tout le cas.

L'application rigoureuse de TDD attire en effet l'attention sur les problèmes de design, par exemple parce que le code est difficile à tester. TDD suggère d'améliorer par du refactoring permanent. TDD ne dit pas comment améliorer concrètement, parmi toute la variété des changements de code possibles.

On couple toujours TDD avec les 4 règles de Simple Design (4RoSD), <a href="http://www.jbrains.ca/permalink/the-four-elements-of-simple-design" target="_blank">présentées sur le site web de Joe Rainsberger</a> :

<blockquote>I define simple design this way. A design is simple to the extent that it:</blockquote>

<ol>
    <li>Passes its tests</li>
    <li>Minimizes duplication</li>
    <li>Maximizes clarity</li>
    <li>Has fewer elements</li>
</ol>

Néanmoins même en suivant TDD à la lettre, avec ces 4 règles et avec la meilleure volonté du monde, on échappe certes au pire car au moins le code est testable ce qui nous donne une option pour le refaire, mais rien n'empêche le design de mal tourner.

Par exemple on peut réduire la duplication avec des fonctions statiques dans des classes Helper, ou en factorisant des bouts de calcul dans des variables locales, par exemple des booléens. On peut aussi ajouter encore des paramètres de plus à une méthode qui en a déjà beaucoup, histoire de la réutiliser dans un nouveau cas. Tout cela semble vérifier les règles, mais c'est du design particulièrement médiocre. Pour beaucoup, dans ces conditions, TDD sera une déception. Les tests sont verts, mais le design est très décevant.

<em>TDD ne suffit pas tout seul pour mener à du bon design. Mais TDD mène vers le bon design. Il est où le truc ?</em>

Joe Rainsberger revendique que TDD se suffit même sans compétences de design a priori. Dans son expérience propre et sa vision, le guidage de TDD et des 4 règles de Simple Design ont suffi à lui montrer ses faiblesses et l'ont guidé vers les bonnes personnes et les bons articles sur les techniques de design à connaître. Il a donc étudié toute cette littérature progressivement, en même temps que sa progression dans la démarche TDD.

<h1>Quand apprendre les compétences en design ?</h1>

On voit à ce point qu'on joue sur les mots, leur compréhension et leur contexte. Mais il n'y a pas vraiment de contradiction. TDD n'est pas auto-suffisant. Les techniques de design sont nécessaires et doivent être apprises et pratiquées à un moment ou à un autre. TDD est un framework qui aide à signaler quand le design est à améliorer.

Vous pouvez décider d'apprendre les techniques de design au préalable. Après tout c'est mon parcours et celui de bon nombre de praticiens qui ont débuté avant 2000, et qui ont appris TDD plus tard. Dans ce cas le risque est de s'attacher aux techniques de design et à leur contexte idéologique parfois dépassé (phase de design préalable et trop longue, UML, abus de patterns, excès de confiance dans ses compétences en design...). Il faudra désapprendre des choses lors du passage à TDD.

L'alternative est de commencer directement par TDD, et d'écouter attentivement les frustrations quotidiennes. Chaque douleur, chaque friction est un signal qui indique une compétence en design qui vous manque. À la façon de Joe Rainsberger, vous pouvez alors fouiller sur le web, ou mieux encore demander conseil à d'autres passionnés lors des multiples Meetups (*) sur le sujet. Cette approche est certainement plus attractive aujourd'hui. Apprendre dirigé par le besoin, dans le bon contexte, et seulement ce qui est pertinent.

C'est aussi notre approche chez <a href="http://www.arolla.fr/formations/" target="_blank">Arolla</a>. TDD d'abord, mais en entremêlant assez tôt les techniques de base de design. L'apprentissage de BDD, des techniques de remédiation de code legacy ou de DDD s'appuient ensuite sur cette base TDD fondamentale.

Si vous faites du TDD ou que vous décidez aujourd'hui de vous y mettre sincèrement, vos compétences en design, orienté object (OO) mais aussi désormais en programmation fonctionnelle (FP) vous seront précieuses pour tirer le meilleur de TDD.

<em>Exemples de meetups parisiens où on peut coder :</em>

<a href="http://www.meetup.com/paris-software-craftsmanship/" target="_blank">Paris Software Craftsmanship</a>

<a href="http://www.meetup.com/Extreme-Programmers-Paris/" target="_blank">Extreme Programming Paris</a>

<a href="http://www.meetup.com/Duchess-France-Meetup/" target="_blank">Duchess Frances</a>

<a href="http://www.meetup.com/Jams-de-code/" target="_blank">Jam de Code</a>

<a href="http://www.meetup.com/Dojo-developpement-Paris/" target="_blank">Dojo Developpement Paris</a>

<a href="http://www.meetup.com/Ladies-Who-Code-Paris/" target="_blank">Ladies Who Code Paris</a>
