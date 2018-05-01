---
layout: post
title: "NCrafts 2016: du code, du design, des principes et des échanges"
date: 2016-06-01 10:15
author: marouane ben amara
comments: true
categories: [Bonnes pratiques de dév, DDD, Evénements, Pair Programming, Programmation, Swift, Ubiquitous Language]
---
Les 12 et 13 mai derniers, j’ai assisté à NCrafts 2016, une conférence internationale autour du <em><a href="http://manifesto.softwarecraftsmanship.org/" target="_blank">Software Craftsmanship</a></em>. Elle s’est déroulée à Paris et on a parlé, entre développeurs, de code, de technos, de carrière, de méthodologie et de bonnes pratiques.

N’ayant pas l’habitude d’assister à des conférences, j’ai trouvé celle-ci bien organisée pour un événement plutôt intimiste et « artisanal ». Certains habitués m’ont d’ailleurs confirmé la croissance de l’édition 2016 par rapport aux précédentes en termes de participation et de moyens.

Durant les 2 jours de conférences, j’ai assisté à des talks, des labs et j’ai parlé à des développeurs que je connaissais (ou pas). Nous le savons tous, le monde est petit et celui des développeurs n’est pas une exception.

Tous les talks et labs ont été filmés, ils seront certainement mis en ligne prochainement sur le site <a href="http://ncrafts.io/" target="_blank">NCrafts.io</a>. Voici ceux qui m’ont le plus marqué :

<h2><b>The Long Road par <a href="https://twitter.com/sandromancuso?lang=fr" target="_blank">Sandro Mancuso</a></b></h2>

C'est un speaker très connu dans le monde du <em>Software Craftsmanship</em>, il a écrit « <a href="https://www.amazon.fr/Software-Craftsman-Professionalism-Pragmatism-Pride/dp/0134052501/ref=sr_1_1?ie=UTF8&amp;qid=1464090137&amp;sr=8-1&amp;keywords=the+software+craftsman" target="_blank">The Software Craftsman</a> » préfacé par <a href="https://twitter.com/unclebobmartin" target="_blank">Robert C. Martin</a>. Il se trouve que par hasard, à cette période-là, je lisais son livre.

Son talk en a repris une bonne partie : il portait sur la carrière d’un « artisan développeur ». Cette dernière est comparée à une longue route bordée d’expériences, d’investissements, de rencontres, de postes...etc. D'ailleurs, Sandro Mancuso considère que les postes occupés, à eux-seuls, ne construisent pas la carrière, et que c’est au développeur lui-même de gérer la sienne et pas à son employeur. Il sera amené à faire des choix de carrière qui seront tout à fait légitimes, à partir du moment où il sera convaincu par son but.

Le talk contenait quelques préconisations plus au moins connues chez les développeurs, j’en cite quelques-unes : l’apprentissage continu, le Pair Programming, les Katas, les blogs, les conférences… Et enfin une partie intéressante avec une touche d’humour sur les anti-patterns au recrutement (comme sur les <em>brainteasers, </em>qu’il déconseille, « <em>Who the f*** cares how many Maltesers I can fit in a station?</em> » :)).

<h2><span style="font-family: inherit"><strong>Extreme Carpaccio slice thin, code fast! Par</strong> <a href="https://twitter.com/dlresende" target="_blank">Diego Lemos</a> <strong>et</strong> <a href="https://twitter.com/aloyer" target="_blank">Arnaud Loyer</a></span></h2>

Ce lab m'a beaucoup intéressé car en plus de son contenu, il s’est déroulé en <em>Pair Programming</em> avec des échanges entre équipes et avec les animateurs. Il s’est agit d’un Kata qui a combiné deux exercices connus : ExtremeStartup et Elephant Carpaccio. L’exercice est disponible sur Github <a href="https://github.com/dlresende/extreme-carpaccio" target="_blank">ici</a>.

On s’y exerce à découper des <em>User Stories</em> le plus finement possible pour partir en Prod le plus vite possible. La vitesse oui, mais sans oublier la qualité (je parle ici de clean code et/ou de tests), car sans cela, l’application devient difficilement maintenable et les évolutions peuvent engendrer des régressions.

A la fin du lab, les discussions ont tourné, entre-autres, autour du découpage choisi par chaque équipe. Ce qui est intéressant, c’est de voir que, même si les participants sont tous des développeurs, lorsqu'on a la casquette de codeur, on préfère livrer l’application entière, alors qu’avec le rôle de Product Owner, on cherche à livrer dès que possible la plus petite fonctionnalité qui permette de gagner de l’argent (de la valeur). En bref, une reproduction assez proche de ce qui nous vivons tous dans nos projets en tant que développeurs.

<h2><strong>Think Sharp, write Swift par</strong> <a href="https://twitter.com/scalbatty" target="_blank">Pascal Batty</a></h2>

N’ayant codé que quelques lignes avec Objective-C, cette session a été une découverte plutôt intéressante. Le talk était structuré selon une comparaison entre C# et Swift.

Les similitudes entre ces deux langages OO et fortement typés facilitent de prime abord le passage de C# vers Swift. L’un d’entre eux, que je trouve intéressant est : les interfaces en C# versus les protocoles en Swift. En revanche, le speaker nous explique que plus on creuse ce langage, plus on se rend compte qu’il ne fonctionne pas tout à fait comme C#, comme par exemple, dans le typage des variables, la gestion des exceptions et des nuls, les boucles for, le compilateur... Il existe d’autres points de fond où Swift a une longueur d’avance sur C#. C’est la programmation fonctionnelle qu’il offre (relativement) grâce aux Extensions/Protocoles et aux Closures.

Moralité, selon le speaker, si l’on souhaite basculer vers Swift, il n'est pas très grave au démarrage de penser C# et de coder Swift, mais pour profiter des avantages offerts par ce langage, il conviendra de penser Swift et de coder Swift. Encore faut-il se munir de l’environnement adéquat : OS X/XCode au mieux, Ubuntu/REPL ou Windows/Ubuntu Bash au pire.

<h2><strong>The Art of Visualising Software Architecte par</strong> <a href="https://twitter.com/simonbrown" target="_blank">Simon Brown</a></h2>

Le speaker revient sur la difficulté que rencontrent beaucoup de développeurs à communiquer visuellement une architecture des SI sur lesquels ils travaillent. Il met en cause deux pratiques. D'une part, l’abandon de l’utilisation d’une modélisation en UML par les développeurs (le sondage rapide qu’il a fait dans la salle de conférence n’a pas réfuté cette réalité), et d’autre part les diagrammes UML eux-mêmes qui peuvent être incompréhensibles voire méconnus. Afin de remédier à ces problèmes, il propose quelques solutions:

<ul>
    <li>L’utilisation normalisée de notations dans les schémas : titres, couleurs, formes, étiquettes…</li>
    <li>L’utilisation d’un modèle avec plusieurs vues et perspectives selon l’audience : une vue logique, une vue de développement, une physique et une quatrième de process.</li>
    <li>L’utilisation d’un « <a href="http://referentiel.institut-agile.fr/ubiquitous.html" target="_blank">Ubiquitous Language</a> » dans le projet ; une notion connue en <a href="https://www.amazon.fr/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215" target="_blank">DDD</a>.</li>
    <li>L’utilisation du « <a href="http://codermike.com/starting-c4" target="_blank">C4 Model</a> » : il s’agit d’une modélisation à 4 niveaux. Elle fait un zoom sur l’architecture d’une vue du contexte jusqu'au diagramme de classes.</li>
</ul>

Il finit son discours sur le besoin de générer une architecture à partir du code (Reverse engineering) et présente la solution <a href="https://www.structurizr.com/" target="_blank">Structurizr</a>.

<h2><strong>Rise of the Machines Automate Your Development par</strong> <a href="https://twitter.com/svenpet" target="_blank">Sven Peters</a></h2>

Encore un talk du « futur » qui nous parle du contrôle des machines sur nos vies, et cette fois-ci, dans nos propres dev ! Trêve de plaisanterie, un talk intéressant parce qu’il parle de vraies solutions déjà existantes qui aident les développeurs dans leur travail quotidien. Il voit de l’automatisation sur plusieurs stades :

<ul>
    <li>Dans la procédure de Setup des environnement de dev.</li>
    <li>Dans les branches de dev avec l'auto-merge, le contrôle des builds...</li>
    <li>Dans la qualité de code (test coverage)</li>
    <li>L'homogénéisation du workflow du développeur avec celui du gestionnaire de tickets.</li>
    <li>La liaison entre l'équipe de dev et l'équipe de support.</li>
    <li>etc</li>
</ul>

<h1><strong>Bilan</strong></h1>

Ce qui est intéressant à NCrafts, ce sont les échanges entre participants et speakers. Les discussions se font aisément entre développeurs de différentes plateformes dans la mesure où c’est une conférence qui porte davantage sur les pratiques que sur des technos. Les labs sont aussi une belle occasion d’échanger des techniques de codage.

<h2></h2>
