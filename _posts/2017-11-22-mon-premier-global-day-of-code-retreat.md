---
layout: post
title: "Mon premier «Global Day of Code Retreat »"
date: 2017-11-22 12:07
author: olfa mabrouki
comments: true
categories: ["#GDRC", Bonnes pratiques de dév, code retreat, Je pense donc je blogue, Pair Programming, Programmation, TDD]
---
<em>Cela fait quelques années qu’on me parle du « Global day of code retreat » comme de l’événement incontournable à faire au moins une fois dans sa vie de développeur : « Olfa, il faut que tu viennes ! Des développeurs du monde entier travaillent sur le même exercice, le même jour ! C’est top ! ». A chaque fois, je me demande : vraiment ? Passer une journée entière à coder après une semaine de travail… ? Il faut être sacrément motivé pour y aller !! Et puis finalement, comme souvent dans ce genre de situation, ma curiosité prend le dessus sur mes doutes. C’est ainsi que je suis retrouvée samedi 18 novembre chez Arolla.</em>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2017/11/coderetreat.jpg"><img src="http://www.arolla.fr/blog/wp-content/uploads/2017/11/coderetreat-1024x768.jpg" alt="coderetreat" width="910" height="683" class="aligncenter size-large wp-image-4835" /></a>

Il est 10h, nous sommes une quinzaine de développeurs aux profils différents (juniors, confirmés et seniors), programmant dans divers langages de programmation : Java, F#, Python, Haskell, etc., armés d’ordinateurs, de café et de croissants.

<h3>Le facilitateur annonce l’ordre du jour :</h3>

Nous allons programmer en binôme par itération de quarante-cinq minutes. Puis, nous supprimerons notre code de l’ordinateur, changerons de binôme et referons le même exercice avec une nouvelle contrainte, technique ou non, après une courte rétrospective. Le Kata en question est le « game of life » dont les règles sont plutôt simples. Le facilitateur fait un rapide rappel sur « TDD », le « pair programming », les quatre principes assurant une conception simple (The 4 principles of simple design) et nous voilà partis !

Sur le moment, le plus frustrant pour moi n’est pas de programmer en binômes avec des inconnus mais plutôt de supprimer le code ! Au quotidien, demander à un développeur de supprimer son code est une énorme punition… Comment pouvons-nous nous débarrasser de notre code, notre bébé, notre fierté ?!!!

<h3>1ère itération : faisons du TDD </h3>

J’essaie de briser la glace avec Nicolas, un développeur Java senior, à l’aise malgré le fait que ce soit, lui aussi, sa première Code Retreat. L’exercice est plutôt facile et agréable. Nous essayons de procéder par approche « inside-out » afin de programmer les différentes règles du « game of life » et là aussi, c’est une première pour nous qui sommes plutôt habitués à l’approche « outside-in ».

<h3>2ème itération : faire attention au « Code smell »  </h3>

La contrainte de cette itération est d’utiliser des classes enveloppes (wrappers) pour encapsuler les primitives et les collections et ainsi éviter le syndrome de “primitive obsessions”. Il nous faut utiliser des objets immuables.

Avec Christophe, mon deuxième binôme, lui aussi développeur senior, nous décidons de programmer en « Python ». Au début, comme je n’ai jamais travaillé sur ce langage, je suis un peu sceptique. Finalement, je parviens à décrypter les instructions grâce aux explications de mon binôme et nous réussissons même à respecter les contraintes techniques imposées !

<h3>3ème itération : faire du « strong-style pairing » </h3>

Cette fois-ci, je binôme avec Cyril, développeur Java junior. L’idée est d’expliquer son point de vue à son binôme et de le pousser à le concrétiser en codant sans prendre le clavier ni alterner la programmation. Cette fois-ci, je remarque que le travail est différent : mon binôme a du mal à lâcher le clavier. Il veut bien faire et tout faire en même temps. C’est une situation par laquelle nous sommes tous passés à un moment de notre vie.  C’est ce qui rend l’exercice enrichissant.

<h3>4ème itération : se bander les yeux</h3>

Une nouvelle contrainte nous tombe dessus : un des membres du binôme doit se bander les yeux pendant 11 minutes tandis que l’autre lui explique ce qu’il est en train de programmer ; puis on inverse les rôles. Cela incite donc Benoît, programmant en Haskell, à m’expliquer un langage que je ne connais pas. Il est à l’aise pour présenter les concepts de base du langage pendant que je ferme les yeux. Je peux poser plein de questions pour essayer de comprendre les différences entre Haskell et Java. Puis, quand c’est au tour de Benoît de fermer les yeux, je lui demande de me guider pour parvenir à coder en Haskell.  Au final, c’est mon itération préférée en termes de communication, travail et d’inconfort :)

<h3>5ème itération : ne pas utiliser de conditions </h3>

Cette fois-ci, le facilitateur nous impose une contrainte technique : ne pas utiliser de conditions. Pas de « if » ni de « switch » …  En changeant de binôme, je me retrouve à nouveau avec un développeur Java senior. L’exercice est plus ou moins facile et nous finissons dans les temps.

<h3>6ème itération : ne communiquer qu’avec le mot « cerveau »</h3>

Pendant cette itération, il est formellement interdit de communiquer avec son binôme à l’aide d’un vocabulaire classique. Le seul mot autorisé pour discuter, expliquer, critiquer, etc. est « cerveau ». La scène est assez comique : d’un seul coup, toute la salle se met à prononcer le mot « cerveau » sur un ton différent, nous sommes comme des enfants et partons dans un fou-rire pas possible... Cet exercice nous force à nous exprimer via la gestuelle plutôt que le verbal. Personnellement, je n’ai pas du tout apprécié cette contrainte et j’avoue même avoir triché en essayant de parler jusqu’à ce que ce le facilitateur me surprenne… J’ai beau avoir rapidement dit « cerveau », je n’étais pas très crédible &#x1f609;

<h3>Mon feedback</h3>

En fin de journée, vers 18h, alors que je m’attendais à être fatiguée, je suis au contraire en pleine forme et super contente d’avoir vécu cette expérience enrichissante.  Nous faisons un tour de table où chacun peut donner son avis sur l’événement : ce qu’il a appris, par quoi il a été surpris et ce qu’il fera lundi.

Personnellement, l’exercice du dépassement de soi et de la sortie de sa zone de confort m’a beaucoup plu. Je considère que l’objectif principal de cette journée a vraiment été de montrer l’importance de la communication au sein d’un binôme tant sur le plan verbal que gestuel.

Cela n’a fait que renforcer mes convictions concernant notre métier de développeur. L’époque du « Geek » enfermé dans sa bulle et qui ne parle qu’à sa machine est bien révolue. De nos jours, nous ne faisons pas que coder. Nous développons au quotidien des compétences dans le relationnel, la psychologie, le développement personnel, etc. Nous sommes des êtres humains qui communiquent, réfléchissent, agissent et ont leurs mots à dire dans les projets.

En conclusion, n’hésitez vraiment pas à aller à ce genre d’événement ! Comme on me l’avait dit : il faut le faire au moins une fois dans sa vie.

Merci à tous les animateurs, organisateurs du « Global day of code retreat » et à Arolla de nous avoir accueillis un samedi.

Olfa
