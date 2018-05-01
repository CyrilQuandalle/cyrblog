---
layout: post
title: Ma rétro sur Scala.io
date: 2013-11-05 11:07
author: jerome prudent
comments: true
categories: [Evénements, Fonctionnel, Programmation, scala, scala.io]
---
J'ai eu la chance de pouvoir participer à la première édition française de scala.io les 24 et 25 Octobre derniers! Et pour celles et ceux qui n'y étaient pas, je vous propose de partager les quelques notes que j'ai prises.

<h2><strong>"Failure: The Good Parts", keynote de <a href="https://twitter.com/viktorklang">Viktor Klang</a></strong></h2>

<div>Viktor Klang commence par énumérer toutes les causes possibles d'erreurs techniques dans le cadre du développement d'une application:</div>

<div>- une <a href="http://research.google.com/pubs/pub35162.html">bug dans la RAM</a></div>

<div>- un disque dur qui crame</div>

<div>- un réseau <a href="http://aphyr.com/posts/288-the-network-is-reliable">pourrave</a></div>

<div><em>"Failure is an Option - Some(failure)"</em></div>

<div>L'erreur à ne pas faire, c'est de devenir parano et de polluer son code avec des try-catch pour gérer tous les cas d'erreur.</div>

<div>On peut "tester" son programme de deux façons :</div>

<div style="padding-left: 30px;">1) <strong>La validation</strong>: est-ce que mon programme fait ce que mes utilisateurs attend de lui ? Ce sont vos tests unitaires et vos scénarios. On teste ce qui est <strong>prévu</strong>.</div>

<div style="padding-left: 30px;">2) <strong>La vérification</strong> (checking) : je stresse mon programme avec des inputs au hasard, et des pannes. On teste l'<strong>imprévu</strong>.</div>

<div>La gestion des erreurs c'est chiant, pénible et bordélique. La meilleur chose à faire c'est de laisser les erreurs arriver et admettre qu'elles existent.</div>

<div>Avec Akka notamment, il existe plusieurs façons de les gérer  :</div>

<div style="padding-left: 30px;">- Réplication</div>

<div style="padding-left: 30px;">- Failover</div>

<div style="padding-left: 30px;">- CircuitBreaker pour éviter de surcharger un système qui est déjà en difficulté</div>

<div style="padding-left: 30px;">- Supervisors</div>

<div style="padding-left: 30px;">- Bulkheading (compartimentation) : la mort d'un sous-système n'entraîne pas l'anarchie chez les autres sous-systèmes.</div>

<div>et tout ça peut être fun :)</div>

<div>Finalement, un système résilient devrait suivre cette adage : <em>"Un escalator n'est jamais en panne, au pire c'est un escalier"</em></div>

<h2><strong>"Real world Akka recipes" par <a href="https://twitter.com/jamie_allen">Jamie Allen</a></strong></h2>

Les bus de communication promettent l'intégrité des messages qui y passent, et parfois, même de la persistance. Cependant, comme on nous l'a bien expliqué pendant cette keynote, cette vision est purement idéale. Dans la réalité, les explosions solaires altèrent les données de nos disques.

On regorge d'autres solutions comme l<a href="https://github.com/eventstore/eventstore/wiki/Event-Sourcing-Basics">'event sourcing</a> ou durable mailboxes mais ils viennent avec leur lot de problèmes.

Jamie Allen propose des patterns implémentables avec Akka non pas pour résoudre nos problèmes mais pour mieux vivre avec.

Notamment le pattern sentinel qui permet de corriger les incohérences dans un système où l'on aurait perdu des messages.

Au niveau du control flow, Jamie passe en revue 3 stratégies:

<p style="padding-left: 60px;">- push with rate limiting : on ne pousse un message à un acteur que toutes les N millisecondes
- push with acknowledgement : on ne pousse un message que lorsqu'on a reçu l'ack du message précédent
- pull from workers to master : les consommateurs demande les messages à leur rythme.</p>

Ensuite il mentionne akka clusters pour créer un système distribué.

Il termine par une démo d'<a href="http://typesafe.com/activator">Activator</a> qui permet de créer son hello akka world en quelques clics.

<h2><strong>"Purely functional IO with Scala" par Runar Bjarnason</strong></h2>

L'intérêt d'être purement fonctionnel est de bénéficier de l'abstraction des fonctions. <code>A -&gt; B</code> est un contrat de service qui garantit que pour un A on aura toujours le même B (referential transparency). Cependant, si une telle fonction doit lire un fichier ou interroger une base de données, elle ne peut plus tenir sa promesse dans tous les cas, elle n'est plus pure.

Faire des IO purement fonctionnels (comme dans Haskell) avec Scala ne relève pas d'un tour de passe passe. Runar Bjarnason nous propose un framework pour faire cela.
Malheureusement les monoids, monads, free monads, world as state, ont eu raison de moi. Décrochage total au bout de 10 minutes.

Note à moi-même: apprendre Haskell ou lire son livre.

<h2><strong>"Playframework Scala: democratising functional programming for web developers", keynote de Sadek Drobi</strong></h2>

<div>Traditionnellement, on est confronté à des applications de type "Single box architecture". Derrière ce terme se cache des applications monolithiques qui savent tout faire et qui ne sont spécialisées en rien. Le terme bounded context n'a pas été cité mais il vient spontanément à celui qui le connaît. Chaque application doit être découpée en micro-services bornés en termes de représentation des données: Prenons l'exemple d'un tweet: si votre service ne s'intéresse qu'aux images des tweets, alors inutile de modéliser toutes les autres données. Le monde moderne est orienté web, se compose de flux continus de données, dans des domaines très variés, qu'il faut agréger et mettre en valeur. Les micro-services doivent être accessibles, traiter des flux infinis de données et être composables, le tout composant votre application.</div>

<div></div>

<div>Ce que propose Play! framework, c'est de répondre à ces besoins de représentation de données, d'accessibilité (http), de haute disponibilité et de composabilité, le tout avec une approche fonctionnelle pas trop effrayante pour les novices, et même une version Java de l'API. Play! peut s'articuler autours de :</div>

<div>- HTTP qui est le protocole universel que l'on sait bien scaler</div>

<div>- MVC parce que ce pattern est simple et structurant</div>

<div>- La programmation fonctionnelle parce que le modèle d'abstraction qu'est une fonction est simple, composable et autodocumenté.</div>

<div>Ensuit le talk s'oriente autours de la composabilité des fonctions.</div>

<div>Sadek nous montre comment traiter des flux de donnée avec le combo <a href="http://www.playframework.com/documentation/2.0/Iteratees">Enumerator/Iteratee</a>, comment les composer, et en extraire de l'information.</div>

<div>Ça me fait bizarrement penser au Feng Shui chinois. Vous devez créer un environnement propice au flot de données (le Qi) et sa mise en valeur. Hmm... je m'égare.</div>

<div>J'ai trouvé <a href="http://engineering.linkedin.com/play/play-framework-democratizing-functional-programming-modern-web-programmers">cet article</a> dont le talk semble être inspiré.</div>

<h2><strong>"Scalaz for the rest of us" par <a href="https://twitter.com/arosien">Adam Rosien</a></strong></h2>

<div>"Si vous avez eu une expérience traumatisante avec scalaz, c'est parce que vous n'aviez pas assez de connaissances en mathématiques." Personnellement j'ai eu peur de Scalaz avant même d'essayer de l'utiliser. Le talk se propose de montrer des cas d'applications pratiques de scalaz plutôt que de faire un cours de math ou de plagier un talk sur Haskell. On nous propose d'introduire scalaz gentiment dans son projet.</div>

<div></div>

<div>Dans les grandes lignes :</div>

<div>

<span style="text-decoration: underline;">Mémoization</span>
La mémoization est une abstraction du concept de cache. Elle permet de tirer partie de la transparence référentielle en wrappant une fonction pour mettre en cache les résultats de calculs. Ainsi, au premier appel d'une fonction, on fait le calcul, et au second on économise son CPU en utilisant la valeur dans le cache.

<span style="text-decoration: underline;">Du sucre!
</span><code>if f() then 1 else 0</code> peut s'écrire <code>f() ? 1 | 0</code>

<code>val a : Some("fo")</code>

<code>a.getOrElse("bar")</code> peut s'écrire <code> a | "bar"</code>

<code>val c = g(f(a))</code> peut s'écrire <code>c = a |&gt; f |&gt; g</code>

<span style="text-decoration: underline;">Domain Validation</span>

Mini-framework pour valider ses objets métier. Très propres

<span style="text-decoration: underline;">Opérations sur de grosses datastructure</span>

J'ai pas super bien compris, du coup je trouve ça hyper compliqué, surtout quand on a goûté à Clojure.

Si vous voulez les détails de code, c'est <a href="https://github.com/arosien/scalaz-base-talk-201208/blob/master/src/landslide/slides.md">ici</a>.

Le talk est pragmatique, lent, et avec du code comme support. On arrive à suivre :)
<h2><strong>Et les autres ?</strong></h2>
Je n'ai malheureusement pas assez de notes mais j'ai aussi assisté à :

<span style="text-decoration: underline;">"Scraping the web with clojure" par <a href="https://twitter.com/abailly">Arnaud Bailly</a></span>

Une vue d'avion de Clojure et de son écosystème.

<span style="text-decoration: underline;">"Pimp my GC - du Scala supersonique" par <a href="https://twitter.com/pingtimeout">Pierre Laporte</a>
</span>

Excellent talk qui explique le fonctionnement du GC, comment mesurer l'impact du GC sur son code et tuner les paramètres. Très riche!
Conclusion: faites des objets immutables (à courte durée de vie), c'est vraiment pas cher en GC.

<span style="text-decoration: underline;">"Extreme speedups and GPGPU: a tale of two practical uses of reified trees" par <a href="https://twitter.com/ochafik">Olivier Chafik</a></span>

J'ai découvert que Scala avait un système de macro. Tout le talk était basé sur cette fonctionnalité, donc je n'ai pas compris grand chose.

<span style="text-decoration: underline;">"Akka in Practice: Designing Actor-based Applications" par <a href="https://twitter.com/agemooij">Age Mooij </a> et <a href="https://twitter.com/RayRoestenburg">Raymond Roestenburg</a></span>

Un hands-on très didactique pour découvrir Akka. On peut même le refaire chez soi, en allant sur son <a href="https://github.com/RayRoestenburg?tab=repositories">repo github</a> (scala-io-exercise-N).

</div>

<h2><strong>Conclusion</strong></h2>

Deux jours de bombardements fonctionnels, asynchrones et résilients. Trop de choses à voir, je n'aurai pas le temps de m'ennuyer ces prochains mois !!

La conf était très bien organisée, les talks de grande qualité, et le public très intéressant. Rendez-vous en 2014.

scala.io
