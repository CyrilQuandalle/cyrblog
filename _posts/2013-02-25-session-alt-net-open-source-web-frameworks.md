---
layout: post
title: Session ALT.NET “Open-source Web Frameworks”
date: 2013-02-25 15:46
author: pierre irrmann
comments: true
categories: [.Net, ALT.NET, Convention over configuration, craftsmanship, DTO, Evénements, HTTP, MVC, Nancy, Programmation, REST, Service Stack]
---
Jeudi dernier, je suis allé assister à la session ALT.NET organisée par Rui (<a href="https://twitter.com/rhwy">@rhwy</a>) et Mathias (<a href="https://twitter.com/mathiaskluba">@mathiaskluba</a>) autour de deux frameworks web open-source de l’éco-système .NET : <a href="http://www.servicestack.net/">ServiceStack</a> et <a href="http://nancyfx.org/">NancyFx</a>. Service Stack et Nancy partent du même besoin de construire des applications et des services REST sans dépendre ni de l’ensemble de l’infrastructure ASP.NET, ni du serveur IIS, et sans mettre en œuvre toute la machinerie de WCF. ASP.NET Web API a déjà fait un grand pas dans cette direction, mais ces alternatives se veulent encore plus simples. Leurs principaux points communs sont :

<ul>
    <li>leurs approches “convention over configuration”</li>
    <li>leur relation étroite avec HTTP (verbes, content-negociation)</li>
</ul>

Rui, toujours partisan de <a href="http://www.meetup.com/paris-software-craftsmanship/">l’artisanat logiciel</a>, expose fièrement un T-shirt “SC with superpowers”, et nous accueille par une présentation des <a href="http://www.meetup.com/altnetfr/">prochains évènements</a> du groupe ALT.NET France et un appel au sponsoring des évènements (le mois prochain, présentations de projets OSS sous forme de lightning talks), avant de laisser la parole à Mathias.

<h3>Service Stack</h3>

La première présentation, par Mathias, tourne autour de Service Stack, qui est une alternative aux solutions Microsoft ASP.NET MVC, Web API pour créer des applications REST et des services. Il s’agit d’un ensemble de briques composables, incluant par exemple :

<ul>
    <li>une gestion de la sérialisation / désérialisation</li>
    <li>un client Redis (stockage sous forme de paires clé / valeur en open-source)</li>
    <li>une gestion de cache (dans <a href="http://redis.io/">Redis</a> ou <a href="http://memcached.org/">Memcached</a>)</li>
    <li>un mapping objet-relationnel (ORMLite)</li>
</ul>

L’ensemble de cette stack est compatible avec MONO (et peut même tourner sur un iPhone, avec tout l’intérêt que ça comporte mais qu’on n’a pas encore trouvé). L’architecture est très orientée DTO (message), par opposition à une vision traditionnelle plutôt orientée RPC (procédure). Service Stack est toujours activement développé et remporte un succès croissant. Mathias nous indique en particulier que le format <a href="https://swagger.io/specification/">Swagger</a>, qui est une forme de description de contrat pour les services REST, sera bientôt intégré dans Service Stack.

Mathias enchaine avec des démos, avec un exemple de service REST dont on parcourt le code coté serveur. En particulier, on y observe une IoC incorporée et l’absence de configuration. Une grande partie du fonctionnement se fait par respect des conventions, mais celles-ci ne sont pas toujours documentées et il peut être un peu dur de s’y retrouver car le non-respect de la convention ne génère aucun message d’erreur, juste une appli qui ne tient pas compte de ce que l’on a fait... Le conseil de Mathias : partir de l’exemple qui fonctionne bien et adapter à ses besoins, plutôt qu’essayer de partir “from scratch” et lutter dans le vide. Nous observons la mise en place de l’injection d’un repository, l’ajout de routes (très simple).

Coté client, les appels sont également très clairs. Dans le cas d’un client et serveur tous les deux en .NET, les DTO sont simplement partagés, ce qui permet de retrouver directement ses objets et de gérer sérialisation / désérialisation automatiquement. Au fur et à mesure des démos, on remarque :

<ul>
    <li>la gestion de credentials (plusieurs providers d’authentification sont utilisables très simplement),</li>
    <li>la notion de validateur de demandes de mises à jour des ressources,</li>
    <li>la gestion de rôles...</li>
</ul>

Les services peuvent être “découverts” par l’intermédiaire d’une page web listant toutes les ressources exposées dans chacun des formats proposés : XML, JSON, CSV, JSV, SOAP (1.1 et 1.2). Visiblement, il n’est pas si facile que ça de sortir du standard, et faire du SOAP consommable depuis un autre client (par exemple WCF) nécessite tout de suite beaucoup plus de configuration.

Service Stack peut aussi être utilisé pour générer du contenu HTML en mode MVC (bien que ce ne soit pas son but premier), et intègre le moteur Razor (qui a été open-sourcé par Microsoft depuis déjà quelque temps). La même URL peut donc être utilisée pour demander différentes représentations d’une même ressource, dont une au format HTML à destination d’utilisateurs humains (parce qu’il n’y a pas que des machines dans la vie !). De la même manière qu’auparavant, la convention doit être respectée : la vue Razor doit porter le même nom que la classe de la ressource.

Pour finir, Mathias insiste sur la quantité de plug-ins disponibles autour de Service Stack. S’en suit une séance de questions / réponses autour des thèmes convention / configuration, REST, Hypermédia, API traversantes…

<h3>Nancy FX</h3>

La deuxième présentation, menée par Rui, traite de NancyFX, qu’il nous vend immédiatement comme “Vachement mieux que Service Stack” !

Rui retrace l’évolution de l’offre web Microsoft ASP.NET dans le passé :

<ul>
    <li>ASP.NET Web Forms : on ne peut rien faire (en termes de personnalisation),<em> “Pas assez de contrôle”</em></li>
    <li>ASP.NET MVC : on peut tout faire (IoC etc…) mais très verbeux dans la personnalisation, beaucoup de configuration et ça n’est pas facile, <em>“Le cout du contrôle est trop élevé”</em></li>
</ul>

Il résume ses envies de développeurs par :

<ul>
    <li>Composition</li>
    <li>Injection</li>
    <li>Convention over configuration</li>
    <li>Comportement par défaut intéressant,</li>
    <li>Autant que possible, une connexion automatique (“auto-wire”) des différents éléments</li>
    <li>Open-source (si possible)</li>
</ul>

Ses besoins sont simples : “Maitrise, Puissance, Simplicité”. Exigeant, tout de même…

Et bien la réponse qu’il nous propose, c’est Nancy : “un framework léger, low-ceremony, pour construire des services basés sur HTTP, en .NET et Mono”. Nancy est directement affilié à <a href="http://www.sinatrarb.com/intro">Sinatra</a>, un framework web créé par la communauté Ruby. La religion de Nancy est la suppression de la “friction” que l’on peut trouver lorsqu’on met en œuvre un framework. Les concepteurs de Nancy cherchent à nous proposer un “super-duper-happy-path” pour construire nos applications : tout est “sympa” à utiliser.

Nancy peut se résumer dans la formule “DSL over HTTP + IoC”, c’est à dire un langage spécialisé au dessus d’HTTP, avec un mécanisme d’inversion de contrôle inclus. Et en plus, ça tourne partout (puisque ça s’appuie sur OWIN) !

S’ensuivent quelques démos, une installation à base NuGet avec zéro configuration, pas de bootstrap à mettre en place. Bien que la démo reste simple, tout est très lisible et mis en œuvre facilement. De par son inspiration provenant du monde Ruby, l’utilisation des dynamic est intensive. Tout est très isolé et décomposé.

Au fur et à mesure des démos, nous découvrons en action :

<ul>
    <li>le moteur de vues intégré par défaut “SuperSimpleViewEngine”, qui permet d’écrire des vues dans une syntaxe rappelant très fortement celle de Razor,</li>
    <li>le conteneur d’injection de dépendance par défaut, TinyIoc, qui permet de faire les opérations classiques d’Ioc, qui fonctionne par défaut avec une injection par constructeur et une résolution par convention de nommage,</li>
    <li>un exemple de développement en TDD,</li>
    <li>la mise en place d’un bootstraper personnalisé,</li>
    <li>le système de diagnostique incorporé au framework et un aperçu de l’espace de gestion du site incorporé (consultation des traces, etc.).</li>
</ul>

De ces démos et des questions correspondantes, j’ai retenu quelques citations qui m’on semblées pertinentes : “Un framework web, au fond, ce n’est que de l’infrastructure” et “Non, ce n’est pas de la magie noire” !

<h3>Conclusion</h3>

Ces deux présentations m’ont permis de voir en action des framework dont j’avais déjà entendu parler mais que je n’avais pas eu l’occasion (ni pris le temps) de tester. De mon point de vue, ces projets nous montrent que c’est à la croisée des systèmes, en s’inspirant de ce qui se fait de mieux ailleurs (ici Ruby et le monde des langages dynamiques) que l’on trouve les solutions intéressantes et innovantes. Le public de développeurs .NET (pour la grande majorité) a semblé très inspiré, et chacun est rentré chez soi avec plus d’idées qu’il n’en avait en arrivant… preuve que cette soirée était une belle réussite !

Pour ceux qui souhaitent d’autres informations et qui passent du temps dans les transports, pleins de références de podcasts, choisissez votre programme préféré :

<ul>
    <li>Service Stack : <a href="http://www.dotnetrocks.com/default.aspx?showNum=843">Dot Net Rocks 843 avec Demis Bellot</a>, <a href="http://thetabletshow.com/?ShowNum=60">The Tablet Show 60 avec John Sonmez</a>, <a href="http://www.hanselminutes.com/300/framework-series-service-stack-with-demis-bellot">Hanselminutes 300 (Demis Bellot)</a> et <a href="http://www.hanselminutes.com/348/more-on-service-stack-with-john-sonmez">348 (John Sonmez)</a>.</li>
    <li>Nancy : <a href="http://herdingcode.com/?p=350">Herding Code 123</a> et <a href="http://herdingcode.com/?p=505">156</a> (Andreas Håkansson and Steven Robbins), <a href="http://www.hanselman.com/blog/HanselminutesPodcast270NancySinatraAndTheExplosionOfNETMicroWebFrameworksWithAndreasH%C3%A5kansson.aspx">Hanselminutes 270 (Andreas Håkansson)</a> et <a href="http://www.hanselminutes.com/351/understanding-nancyfx-with-richard-cirerol">351 (Richard Cirerol)</a></li>
</ul>

<a href="https://twitter.com/pirrmann">@pirrmann</a>
