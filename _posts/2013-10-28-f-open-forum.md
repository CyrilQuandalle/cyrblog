---
layout: post
title: F# Open Forum
date: 2013-10-28 11:19
author: pierre irrmann
comments: true
categories: [Actu, Evénements, F#, Fonctionnel, fonctionnel, meetup, Programmation]
---
<p align="justify"><img class="alignright size-full wp-image-1929" style="margin: 5px;" alt="f-sharp-meetup-320" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/f-sharp-meetup-320.jpeg" width="320" height="240" />C’est avec un très grand enthousiasme que je me suis rendu lundi 21 octobre <a href="http://www.meetup.com/Functional-Programming-in-F/events/142127922/">au premier meetup F# organisé à Paris</a>, dans les locaux d’Arolla. Cela fait déjà deux ans que j’ai eu l’occasion de participer à <a href="http://www.arolla.fr/blog/2011/12/formation-f-avec-robert-pickering/">une formation F#, également organisée par Arolla</a>. Depuis, mon intérêt pour la programmation fonctionnelle n’a pas cessé de s’accroitre.</p>
<p align="justify">Tout d’abord, je tiens à remercier <a href="https://twitter.com/tjaskula" target="_blank">Thomas</a> pour son initiative d’organiser ce groupe meetup, auquel je souhaite toute la réussite possible. La création de ce groupe français a reçu un accueil très positif, non seulement à Paris mais aussi à l’international, et j’espère que nous aurons la chance d’avoir dans les mois qui viennent des invités prestigieux !</p>
<p align="justify">Ce premier rassemblement a attiré les pratiquants les plus visibles de la communauté, comme <a href="https://twitter.com/robertpi" target="_blank">Robert Pickering</a> et <a href="https://twitter.com/thinkb4coding" target="_blank">Jérémie Chassaing</a>, mais aussi de nombreuses personnes heureuses d’échanger sur le sujet de la programmation fonctionnelle, et de F# en particulier. L’idée aujourd’hui était de prendre un premier contact avec la communauté, d’identifier les attentes et les envies des uns et des autres.</p>

<h2>Un foisonnement d’idées</h2>
<p align="justify">Après une courte introduction de Thomas, chacun s’est présenté et a exposé la motivation de sa présence, puis nous avons distribué des post-it et répertorié des sujets que nous aimerions voir aborder dans les prochains événements. En vrac :<img class="size-full wp-image-1930 alignright" style="margin: 5px;" alt="f-sharp-meetup-2-320" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/f-sharp-meetup-2-320.jpeg" width="240" height="320" /></p>

<ul>
	<li>Parallélisme en F#</li>
	<li>F# pour le web</li>
	<li>DSLs &amp; parsor combinators</li>
	<li>Structure d’un projet</li>
	<li>Retours d’expérience</li>
	<li>Modèle acteurs</li>
	<li>DDD - CQRS - EventSourcing</li>
	<li>Undertone</li>
	<li>FsUnit</li>
	<li>F# pour le développeur C#</li>
	<li>F# pour le développeur OCaml</li>
	<li>Hands-on &amp; katas</li>
</ul>
Pour la suite de la soirée, deux thèmes ont été retenus par les participants.
<h2>Asynchronisme et modèle acteurs</h2>
<p align="justify">La première discussion a porté sur le modèle de programmation asynchrone en F#. Jérémie s’est lancé sur le fonctionnement de <em>async</em> en F#, les différences de conception avec le <em>async</em> de C# 5, et les possibilités de composition des workflows asynchrones. Le côté “parallélisme” n’a au final pas été vraiment évoqué, mais il s’agissait ici d’une discussion non préparée ! Le sujet s’est ensuite déplacé vers le modèle acteurs, en expliquant le fonctionnement des MailboxProcessor. Par la suite, <a href="https://twitter.com/jdelh" target="_blank">Julien Delhomme</a> a pris la parole pour exposer les limitations de ce fonctionnement par rapport à un modèle acteurs intégré au langage, comme en Erlang, ou à une bibliothèque plus aboutie, comme Akka en Scala. En F#, le projet à regarder sur le même sujet semble être FSharpActors. Le sujet a captivé la salle, et je ne doute pas qu’une présentation plus complète puisse être organisée sur le même thème dans le futur.</p>

<h2>DSLs et parsor combinators</h2>
<p align="justify">Les DSL (<a href="http://en.wikipedia.org/wiki/Domain-specific_language">Domain Specific Language</a>) sont classiquement séparés entre DSL internes et externes, selon qu’ils s’expriment dans la syntaxe d’un langage hôte ou définissent leur grammaire propre. F# est reconnu comme un excellent langage pour créer ses propres DSL internes, notamment par sa capacité à manipuler facilement des AST (Abstract Syntax Trees). L’utilisation de Discriminated Unions et de pattern matching rend en effet la manipulation d’arbre beaucoup plus simple et puissante qu’elle peut l’être avec une représentation objet avec un pattern visiteur. Jérémie a mentionné qu’il avait manipulé des AST en F# pour <a href="http://thinkbeforecoding.com/post/2010/10/19/Duck-Delete-Update-Create-Killer">son projet Duck</a>, et que la différence avec C# était flagrante.</p>
<p align="justify">Après ces échanges assez généraux sur le sujet, Jérémie a refait un tour au tableau pour nous expliquer succinctement le fonctionnement des computation expressions, et la possibilité de construire un “expression builder” pour construire un arbre syntaxique. Comme pour le sujet précédent, un présentation plus complète avec des exemples de code semble toute indiquée !</p>

<h2>Prochains rendez-vous</h2>
<p align="justify">Un premier sujet est déjà dans les tuyaux, au cours duquel Robert va nous proposer le talk qu’il aura préparé pour la conférence BuildStuff, “<a href="http://www.meetup.com/Functional-Programming-in-F/events/147391352/">Expression Orientend Programming with F#</a>”. Et au moment de la rédaction de ce billet, une autre présentation semble également se profiler au mois de décembre par Mathias Brandewinder…</p>
<p align="justify">En ce qui me concerne, je suis très motivé pour animer des initiations à F#, constituées d’une courte partie théorique, et de premiers exercices sélectionnés pour permettre de rentrer dans le monde de la programmation fonctionnelle. Si vous êtes intéressés pour participer à un évènement comme celui-ci, n’hésitez pas à laisser un commentaire sur le <a href="http://www.meetup.com/Functional-Programming-in-F/" target="_blank">meetup</a> ou à me contacter directement sur Twitter.</p>
<a href="https://twitter.com/pirrmann" target="_blank">@pirrmann</a>
