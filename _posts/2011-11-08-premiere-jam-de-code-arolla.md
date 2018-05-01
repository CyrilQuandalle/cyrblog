---
layout: post
title: Première Jam de code Arolla
date: 2011-11-08 15:34
author: cyrille martraire
comments: true
categories: [Agilité, arolla, Bonnes pratiques de dév, code kata, code legacy, Evénements, jam, Programmation, TDD]
---
<p style="text-align: justify;">Jeudi 6 octobre avait lieu la première édition des "Jams de Code". En référence aux Jam Sessions entre musiciens, il s'agit de se retrouver pour partager et pratiquer ce qui nous intéresse.</p>

<blockquote>"Amenez votre laptop et votre langage préféré, nous fournissons la bière et les chips."</blockquote>

<p style="text-align: justify;">Lors de cette première nous étions 4 développeurs motivés sur 6 inscrits, ce qui est un nombre pair, parfait pour faire du pair-programming !</p>

<h2 style="text-align: justify;">Code Katas</h2>

<p style="text-align: justify;">Nous avons choisi un code kata "Supermarket Pricing", qui commence sans code:</p>

<blockquote>"Some things in supermarkets have simple prices: this can of beans costs $0.65. Other things have more complex prices. For example:
<ul>
    <li>three for a dollar (so what’s the price if I buy 4, or 5?)</li>
    <li>$1.99/pound (so what does 4 ounces cost?)</li>
    <li>buy two, get one free (so does the third item have a price?)"</li>
</ul>
</blockquote>

<p style="text-align: justify;">Le sujet est rapidement passionnant, et on découvre une profondeur insoupçonnée à une problématique qui semblait banale. Celles et ceux familiers avec la finance ou le pricing des forfaits de téléphonie reconnaitront aussi des enjeux similaires (arrondis, règles de comptage...).</p>

<p style="text-align: justify;">Après pas mal de discussions et d'idées (tellement de bonnes idées que ça me donnait envie de tout noter), mais aucune conclusion concrète, il était temps de se frotter à la réalité du code ! Un binôme en C# 4.0, un autre en Java, nous avons commencé le même exercice en code, test par test.</p>

<h1 style="text-align: justify;"></h1>

<h2 style="text-align: justify;">TDD as if you meant it and Tranformation Priority Premise</h2>

<p style="text-align: justify;">Nous avons tous fait du TDD en code de production, mais pas tant que ça en TDD extrême. Commencer par un "Canary Test" pour vérifier l'environnement, puis ajouter un premier test ultra trivial, puis un autre encore trivial, et ainsi de suite. C'est lent, et c'est justement la raison de pratiquer en dehors de la pression du bureau.</p>

<p style="text-align: justify;">Ensuite, on se retient de sauter sur des abstractions trop rapidement: pas besoin d'introduire de classe, une fonction suffit. Et dans le style <a href="http://gojko.net/2009/02/27/thought-provoking-tdd-exercise-at-the-software-craftsmanship-conference/">TDD as if you meant it</a> on commence par écrire une implémentation dans le test. On s'oblige à une assertion par test. On consacre du temps à bien nommer chaque test.</p>

<p style="text-align: justify;">Ce qui était très sympa en étant peu nombreux et face à face, c'est qu'on pouvait échanger en direct entre binômes sur nos hésitations et conseils, dans une sorte de "pairing à basse fréquence entre pairs à haute fréquence".</p>

<p style="text-align: justify;">On a aussi évoqué et tenté de respecter l'idée de <a href="http://cleancoder.posterous.com/the-transformation-priority-premise">Tranformation Priority Premise</a> proposée par Robert Martin, qui consiste à ne jamais sauter d'étape dans l'élaboration d'une implémentation: "on refuse de prendre une décision [ajouter du code d'une complexité supérieure] tant que le test ne nous y oblige pas".</p>

<h1 style="text-align: justify;"></h1>

<h2 style="text-align: justify;">Remarques</h2>

<p style="text-align: justify;">On estime souvent que l'exercice de notre métier ne permet pas une pratique suffisante; j'étais néanmoins surpris quand Mathieu nous dit avoir codé les "Roman Numerals", non pas comme un kata mais pour son employeur, et néanmoins en TDD, test après test ! Ça me rappelle que le choix d'une mission ou d'un poste qui offre des vrais défis en est un véritable plus pour progresser plus rapidement à effort égal.</p>

<h1 style="text-align: justify;"></h1>

<p style="text-align: justify;">Lors de la mini rétrospective finale nous avons comparé le temps passé à discuter de possibles solutions avec le temps passé à faire grossir une implémentation guidée par les tests, avec le sentiment que l'approche par les tests, bien que lente, progresse au moins mécaniquement vers une conclusion, ce qui n'est pas le cas des discussions "en l'air". Néanmoins de telles discussions sont passionnantes et finalement assez proches d'un brainstorming, qui a aussi son utilité.</p>

<p style="text-align: justify;">Nous nous sommes quittés en discutant de nos techniques pour intervenir sur du code legacy. Il s'agit d'un vaste sujet qui revient en force sur le devant de la scène, et c'est tant mieux car c'est un enjeu majeur de notre métier !</p>

<p style="text-align: justify;">Rendez-vous le<strong> premier jeudi de chaque mois à 19h30 au siège d'Arolla</strong> (21 rue du Bouloi, Paris 1er) <strong>pour participer à ces Jams de code</strong>!</p>
