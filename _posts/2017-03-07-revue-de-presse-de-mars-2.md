---
layout: post
title: Revue de presse de mars
date: 2017-03-07 12:27
author: lionel tougne
comments: true
categories: [java, java8, java9, javascript, jvm, Programmation, Revues de presse, scala]
---
Pour cette revue de presse de mars, nous commencerons par une giboulée de Java. Puis nous nous attaquerons à la problématique d’écriture d’un logiciel : pourquoi est-ce difficile ? S’ensuivra un cas pratique sur la performance. Et enfin un dernier cas pratique dont le sujet sera la réécriture d’une application afin d’en améliorer les performances, l’architecture, etc.

&nbsp;

<h3>Java, c’est plus fort que toi ! ou pas …</h3>

&nbsp;

Depuis Java 8, nous avons les objets Optional pour nous éviter les Null Pointer Exception. Mais ces derniers cassent les règles qui définissent les monades. Au travers de cet article, l’auteur nous explique pourquoi ces règles sont importantes. En même temps, il remet en lumière l’utilité d’une monade, sans rentrer dans le détail théorique.

&nbsp;

<a href="https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/" target="_blank">https://www.sitepoint.com/how-optional-breaks-the-monad-laws-and-why-it-matters/</a>

&nbsp;

Maintenant, attardons-nous sur une critique récurrente de la jvm : sa lourdeur et ce que l’on entend par « lourdeur ». En effet, c’est un terme subjectif qui a deux sens majeurs. Premièrement le sens de poids (taille des librairies, consommation de la mémoire, etc.), deuxièmement le sens de la vitesse (vitesse d’exécution du programme, vitesse de développement, etc.).

&nbsp;

<a href="https://www.opensourcery.co.za/2017/01/05/the-jvm-is-not-that-heavy/" target="_blank">https://www.opensourcery.co.za/2017/01/05/the-jvm-is-not-that-heavy/</a>

&nbsp;

Regardons du côté du futur du langage Java avec Java 9. Dans le billet suivant, il est question des nouveautés ajoutées aux Optional et aux Stream.

&nbsp;

<a href="https://aboullaite.me/java-9-enhancements-optional-stream/" target="_blank">https://aboullaite.me/java-9-enhancements-optional-stream/</a>

&nbsp;

<h3>Dur dur d’écrire un programme !</h3>

&nbsp;

Écrire du logiciel, on l’oublie souvent, mais ce n’est pas si facile ! Il est donc question, dans l’article suivant, de rappeler quelques faits. Même si cela peut paraître banal.

&nbsp;

<a href="https://m.signalvnoise.com/writing-software-is-hard-388d5e982ad9#.119dymbjq" target="_blank">https://m.signalvnoise.com/writing-software-is-hard-388d5e982ad9#.119dymbjq</a>

&nbsp;

Le temps qu’une page web met à s’afficher est très important. Dans cet article, c’est de JavaScript qu’il est question. L’auteur parle de ce qui compte pour optimiser votre temps d’affichage. On discute aussi de ce qui est fait du côté du navigateur pour grappiller de précieuses secondes à l’exécution des scripts JavaScript.

&nbsp;

<a href="https://medium.com/dev-channel/javascript-start-up-performance-69200f43b201#.3d5llxmjn" target="_blank">https://medium.com/dev-channel/javascript-start-up-performance-69200f43b201#.3d5llxmjn</a>

&nbsp;

Nous terminerons cette revue de presse, par la réécriture d’une application en Scala. Dans ce billet, il est expliqué la raison de cette révision et quels bénéfices on peut en tirer.

&nbsp;

<a href="http://making.duolingo.com/rewriting-duolingos-engine-in-scala" target="_blank">http://making.duolingo.com/rewriting-duolingos-engine-in-scala</a>
