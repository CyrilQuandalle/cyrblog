---
layout: post
title: Commentaires sur les commentaires
date: 2013-01-14 10:25
author: christophe michel
comments: true
categories: [commentaires, Programmation, qualité]
---
<p style="text-align: justify;">Lorsqu’on vous a parlé de qualité de code, on vous a sûrement déjà parlé des commentaires. On vous a peut-être dit qu’il était important d’avoir du code bien commenté, voire abondamment commenté.</p>

<p style="text-align: justify;">Alors vous ouvrez votre IDE, vous le configurez aux petits oignons et vous commencez à comm...</p>

<h1 style="text-align: justify;">Minute !</h1>

<p style="text-align: justify;">Tous les commentaires ne sont pas nés égaux. En fait le code source de la plupart des applications est encombré de commentaires parfaitement inutiles et il en découle que des commentaires en quantité ne signifient pas que le code est correctement documenté.</p>

<p style="text-align: justify;">Il existe de nombreux types de commentaires néfastes, mais il y en a deux qui en constituent la grande majorité : les radoteurs et les menteurs.</p>

<p style="text-align: justify;">Les premiers se reconnaissent immédiatement :</p>

<p style="text-align: justify;"></p>

<p style="text-align: justify;">Souvent générés automatiquement, ces commentaires n’apportent strictement aucune information. A bannir, donc.</p>

<p style="text-align: justify;">Les menteurs quant à eux posent un autre problème :</p>

<p style="text-align: justify;"></p>

<p style="text-align: justify;">Rien ne vous choque ? Il a y contradiction entre le code et le commentaire. Écrire des commentaires a un prix, ils doivent être maintenus pour rester cohérents avec le code. C’est pour cette raison qu’on ne doit commenter du code que lorsque c’est indispensable à sa compréhension.</p>

<h1 style="text-align: justify;">Informer sans commenter</h1>

<p style="text-align: justify;">Le point clé pour ne pas avoir à écrire des commentaires qui risquent de devenir obsolètes, c’est d’écrire du code qui s’explique tout seul. Cela veut dire exprimer clairement ses intentions au travers du code.</p>

<p style="text-align: justify;">Le choix des noms dans le code est primordial car ils portent la plus grande part de l’information sur l’intention du développeur.</p>

<p style="text-align: justify;">Les noms de classes, de fonctions et de variables doivent avoir du sens, être clairs et sans ambiguïté. Idéalement, on cherchera à avoir des noms prononçables et concis.</p>

<p style="text-align: justify;">Voici deux fonctions qui font exactement la même chose, néanmoins la seconde est compréhensible sans qu’il n’y ait besoin d’explications supplémentaires.</p>

<p style="text-align: justify;"></p>

<p style="text-align: justify;">Il est également recommandé de maintenir une certaine cohérence pour les noms au travers de l’application, ce qui renforce la compréhension. On veillera par exemple à utiliser les verbes (de type “fetch”, “get”, “delete”, “update”) de la même façon dans tout le code.</p>

<h1 style="text-align: justify;">Améliorer la structure</h1>

<p style="text-align: justify;">Pour rendre plus explicite un programme, on peut jouer sur une autre dimension et faire émerger des noms dans le code par refactoring : extraction d’expression dans des variables locales, extraction de code dans de nouvelles méthodes ou de nouvelles classes sont autant d’occasions de mettre un nom explicite sur une portion de code.</p>

<p style="text-align: justify;">Cette technique permet d’expliquer simplement des expressions régulières (souvent cryptiques pour qui n’a pas l’habitude) ou des expressions booléennes complexes :</p>

<p style="text-align: justify;"></p>

<p style="text-align: justify;">Ce travail est applicable à toutes les échelles de l’application et peut donc être appliqué sur des portions très locales, dans une perspective d’amélioration d’un existant. Faire l’effort de donner un nom à un concept oblige à réfléchir à sa cohérence et sa bonne formalisation : lorsqu’il est difficile de trouver un nom, c’est en général signe d’un problème dans la conception (le cas est criant pour les classes et permet de détecter rapidement les violations du Single Responsibility Principle)</p>

<h1 style="text-align: justify;">Un peu d’amour quand même</h1>

<p style="text-align: justify;">Si minimiser la part des commentaires dans son application est avisé, il ne s’agit pas pour autant de les éradiquer. Ils restent un outil indispensable au développeur.</p>

<p style="text-align: justify;">Ils permettent de fournir de l’information “hors code” (prévenir qu’un test unitaire est très long à exécuter), placer des marqueurs à destination du développeur (TODO, FIXME, à utiliser avec parcimonie) et parfois de clarifier un peu plus l’intention, comme par exemple de dire quel algorithme a été utilisé.</p>

<p style="text-align: justify;">Par ailleurs, dès lors que l’on travaille sur une API publique, une bonne documentation via commentaires (javadoc notamment) est indispensable.</p>

<p style="text-align: justify;">En attendant, n’oubliez jamais, le code est écrit pour être lu !</p>
