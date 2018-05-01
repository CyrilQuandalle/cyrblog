---
layout: post
title: "Architectures, librairies et performance : La revue de presse de décembre"
date: 2017-12-15 13:02
author: lionel tougne
comments: true
categories: [architecture, librairie, monad, monoïd, perfomance, Programmation, Programmation, revue de presse, Revues de presse]
---
Dernière revue de presse de l’année ! Pour terminer 2017 en beauté, parlons architectures, librairies, &amp; performance.

Commençons par un « gros » mot : Monad.

Mais attention ! Pas question de le définir par d’obscures formules mathématiques. Ici, l’auteur définit ce qu’est une Monad au sens pratique du terme et explique surtout comment implémenter un DSL dans le langage OCAML permettant d’obtenir l’effet souhaité.

<a href="http://okmij.org/ftp/tagless-final/nondet-effect.html">http://okmij.org/ftp/tagless-final/nondet-effect.html</a>

Monad à ne pas confondre avec Monoïd ! Retrouvez toutes les explications concernant les monoïdes en visionnant le talk de Cyrille Martraire donné à Devoxx en 2015 : <a href="https://www.youtube.com/watch?v=_jr8E5GVnBA&t=176s" rel="noopener" target="_blank">https://www.youtube.com/watch?v=_jr8E5GVnBA&amp;t=176s</a>

Un petit tour du côté du debugging avec un retour d’expérience : l’équipe en question ne comprenait pas pourquoi sa requête en base tournait beaucoup plus vite que prévu et ne savait pas comment faire en sorte qu’elle ne plante pas. Il se trouve qu’après analyse, une fois de plus, le diable se cache dans les détails. Le souci venait du compilateur « Just In Time » de la JVM qui faisait sa propre optimisation.

On oublie très souvent que la JVM nous offre beaucoup de choses et que cela peut nous aider comme nous surprendre…

<a href="https://databricks.com/blog/2017/02/16/processing-trillion-rows-per-second-single-machine-can-nested-loop-joins-fast.html">https://databricks.com/blog/2017/02/16/processing-trillion-rows-per-second-single-machine-can-nested-loop-joins-fast.html</a>

Très souvent, dans une interface utilisateur, il existe une fonction recherche.

La mise en place semble « simple » à première vue tant les outils pour effectuer cette tâche sont nombreux. Mais ne pas connaître les rouages peut mener à de gros problèmes lors de l’implémentation d’une solution existante.

Dans l’article suivant, l’auteur tente de faire un panorama des algorithmes de recherches. Avec les contraintes qu’il faut connaître et intégrer.

<a href="https://medium.com/startup-grind/what-every-software-engineer-should-know-about-search-27d1df99f80d">https://medium.com/startup-grind/what-every-software-engineer-should-know-about-search-27d1df99f80d</a>

Enfin, parce que c’est Noël, lâchons un peu le clavier et remettons-nous à l’électronique. Que savez-vous de la loi d’ohm ? Des semi-conducteurs ?  Des filtres ?

Bref, voilà de quoi éclairer votre sapin de Noël. &#x1f609;

<a href="http://lcamtuf.coredump.cx/electronics/">http://lcamtuf.coredump.cx/electronics/</a>

Passez de Bonnes Fêtes !

<a href="http://www.arolla.fr/blog/wp-content/uploads/2017/12/bonnesfetes.jpg"><img src="http://www.arolla.fr/blog/wp-content/uploads/2017/12/bonnesfetes.jpg" alt="bonnesfetes" width="500" height="500" class="aligncenter size-full wp-image-4844" /></a>
