---
layout: post
title: Formation F# avec Robert Pickering
date: 2011-12-07 09:41
author: pierre irrmann
comments: true
categories: [Actu, F#, Fonctionnel, fonctionnel]
---
<p align="JUSTIFY">Comment mieux illustrer l’orientation d’Arolla vers les langages fonctionnels, qu’en décrivant la formation à laquelle je viens de participer?</p>
<p align="JUSTIFY"> Robert Pickering, MVP F#, intervenant régulier lors d’évènements F#, et auteur d’un des premiers livres sur le langage F# : “<span style="color: #000099;"><span style="text-decoration: underline;"><a href="http://www.amazon.com/Beginning-F-Robert-Pickering/dp/1430223898/">Beginning</a><a href="http://www.amazon.com/Beginning-F-Robert-Pickering/dp/1430223898/"> F</a><a href="http://www.amazon.com/Beginning-F-Robert-Pickering/dp/1430223898/">#</a></span></span>”, a animé pour la première fois en France ses formations “Beginning F#” et “Building Applications with F#”, pour une poignée d’heureux élus d'Arolla dont j’ai eu la chance de faire partie.</p>
<p align="JUSTIFY">F# est un des langages supportés par le Framework Microsoft .NET. Il s’agit d’un langage de premier ordre, intégré à Visual Studio 2010. F# est un langage résolument fonctionnel, et il présente en ce sens de nombreuses divergences avec les langages plus “traditionnels” de la plateforme .NET, C# et VB.NET, orientés objet.</p>
<p align="JUSTIFY"> En ce qui me concerne, j’avais déjà eu un premier contact avec le langage F# à travers la lecture (pas encore terminée à ce jour) du très bon “Real-World Functional Programming with examples in F# and C#”, de Tomas Petricek, <span style="color: #000099;"><span style="text-decoration: underline;"><a href="http://www.manning.com/petricek/">disponible</a><a href="http://www.manning.com/petricek/"> chez</a><a href="http://www.manning.com/petricek/"> Manning</a></span></span> en ebook (Arolla = green). Cette formation m’a permis d’entrer de plein pied dans l’utilisation de F#, et je me propose de vous en donner ici un compte-rendu.</p>
<p align="JUSTIFY">La formation a été organisée en deux sessions : une introduction de 2 jours à la programmation fonctionnelle et à F#, puis 4 semaines plus tard une mise en pratique plus concrète, à nouveau sur 2 jours.</p>
<p align="JUSTIFY"> La première session, “Beginning F#”, a permis aux participants, consultants Arolla chevronnés du C#, de découvrir les principaux concepts de la programmation fonctionnelle et leur mise en oeuvre en F#. Pour la plupart, nous étions déjà sensibilisés à la programmation fonctionnelle (certains d’entre nous ayant participé à l’Aroll@fterwork de Cyrille Martraire, directeur technique d'Arolla, auquel Robert était d'ailleurs présent), et les concepts d’immutabilité ou de “higher order functions” n’étaient pas totalement nouveaux pour nous. Le succès de LINQ a également démocratisé certains concepts par exemples au travers des délégués typés génériques tels que Action&lt;T&gt; et autres Predicate. Cependant F# propose bien d’autres notions auxquelles nous n’étions pas familiarisés comme par exemple les “Union Types”, les “Record Types”, le “Pattern Matching”, ou l’application partielle de fonctions. Je reprends ici un schéma présenté par Robert, et qu’il a également publié dans <span style="color: #000099;"><span style="text-decoration: underline;"><a href="http://strangelights.com/blog/archive/2011/08/26/towards-a-better-taxonomy-of-programming-languages.aspx">un</a><a href="http://strangelights.com/blog/archive/2011/08/26/towards-a-better-taxonomy-of-programming-languages.aspx"> article</a><a href="http://strangelights.com/blog/archive/2011/08/26/towards-a-better-taxonomy-of-programming-languages.aspx"> de</a><a href="http://strangelights.com/blog/archive/2011/08/26/towards-a-better-taxonomy-of-programming-languages.aspx"> son</a><a href="http://strangelights.com/blog/archive/2011/08/26/towards-a-better-taxonomy-of-programming-languages.aspx"> blog</a></span></span>, et qui montre bien les apports spécifiques de F# par rapport au C# et au VB.NET.</p>
<p align="LEFT"> <a href="http://www.arolla.fr/blog/wp-content/uploads/2011/12/Sch%C3%A9ma.png"><img class="aligncenter size-full wp-image-172" title="Schéma" src="http://www.arolla.fr/blog/wp-content/uploads/2011/12/Sch%C3%A9ma.png" alt="" width="498" height="374" /></a></p>
<p align="JUSTIFY">La formation se déroule par étape et est rythmée par de fréquents exercices d’application qui permettent de mieux s’approprier les sujets traités. De mon point de vue, les plus grandes difficultés rencontrées ne sont pas de l’ordre de la compréhension mais plutôt dans l’abandon des habitudes de la programmation impérative et des automatismes acquis au fil des années de pratique, pour entrer dans la démarche de la programmation fonctionnelle. Les syntaxes qui au début semblent très confuses se révèlent être au final très expressives, et le code que l’on écrit nous apparaît au final plus élégant et adaptable.</p>
<p align="JUSTIFY"> Nous avons terminé la première session par l’implémentation d’intelligences artificielles de fourmis (ou plutôt sur des tentatives d’amélioration d’IA existantes), que nous avons ensuite confrontées les unes aux autres. Cet exercice a ramené tout le monde quelques années en arrière, et chacun a pu se rappeler pourquoi il avait choisi de faire ce beau métier ! :)</p>
<p align="JUSTIFY">Mon sentiment au bout de la première session de 2 jours, était d’avoir mis un pied dans un autre univers, mais de ne pas encore savoir si je pouvais en ramener autre chose qu’un souvenir à mettre sur une étagère. Les exemples traités étaient effectivement intéressants, mais de là à voir comment réaliser réellement une application en F#, il restait un fossé important.</p>
<p align="JUSTIFY">Au bout de quelques semaines, la deuxième session est venue corriger tout cela en développant en quatre demi-journées, 4 sujets différents, basés sur des cas concrets :</p>

<ul>
	<li>
<p align="JUSTIFY">une application e-commerce en ASP.NET MVC 3,</p>
</li>
	<li>
<p align="JUSTIFY">une étude sur des statistiques d’achat de produits pour rechercher des similarités entre acheteurs et proposer de recommandations,</p>
</li>
	<li>
<p align="JUSTIFY">un client Twitter en WPF, avec pour objectif d’identifier dans des flux de messages des conversations (de ensembles d’échanges bilatéraux de messages entre utilisateurs, en réponses les uns aux autres),</p>
</li>
	<li>
<p align="JUSTIFY">une étude sur les résultats de tirages d’un jeu de hasard.</p>
</li>
</ul>
<p align="JUSTIFY"> Chacun de ces cas était découpé en exercices de complexité croissante, sur des solutions préparées à l’avance et permettant d’obtenir en fin d’exercice des résultats visibles. Heureusement, les solutions des exercices étaient également incluses dans les dossiers fournis par Robert car il a tout de même réussi à nous perdre à plusieurs reprises.</p>
<p align="JUSTIFY">Pour conclure, je suis très satisfait d’avoir pu prendre part à cette formation, et j’espère avoir très bientôt l’occasion de mettre en pratique ces connaissances acquises. Je ne manquerai pas de vous en faire profiter sur le blog Arolla !</p>
<p align="JUSTIFY"><a href="http://twitter.com/#!/pirrmann">@pirrmann</a></p>
