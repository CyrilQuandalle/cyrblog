---
layout: post
title: What Is Clean Code?
date: 2013-11-14 12:28
author: ilyas saqrani
comments: true
categories: [Bonnes pratiques de dév, clean code]
---
<p style="text-align: justify;"><em>(Résumé du début du livre <strong>Clean Code</strong> de Robert C. Martin)</em></p>
<p style="text-align: justify;">Rappelons-nous que le code est vraiment la langue dans laquelle nous exprimons en fin de compte les exigences.</p>
<p style="text-align: justify;">N’importe quel logiciel est constitué de code, nous pouvons créer des langages qui sont au plus près des besoins business, nous pouvons créer des outils qui nous aident à analyser et traduire ces exigences, mais il y aura toujours du code, et plus ce code est clean plus le logiciel est maintenable et évolutif.</p>
<p style="text-align: justify;">Mais comment doit être un code clean? C’est le but de cet article.</p>
<p style="text-align: justify;"><b><i>Bad Code</i></b></p>
<p style="text-align: justify;">Vous avez déjà été considérablement gêné par le mauvais code, vous avez sûrement senti cet obstacle plusieurs fois, mais pourquoi l’avez-vous écrit ? Si vous êtes développeurs, vous avez forcément écrit un jour du mauvais code, c’est inévitable, et les raisons sont multiples, et certainement bien claires dans vos têtes:</p>

<ul style="text-align: justify;">
	<li>Essayer d'aller vite.</li>
	<li>Pas de temps à perdre sur cette feature.</li>
	<li>Vous n'avez pas eu le temps de faire un bon travail.</li>
	<li>Votre patron serait en colère contre vous si vous preniez le temps de nettoyer votre code.<b></b></li>
	<li>Peut-être que vous étiez fatigué de travailler sur ce programme et vous vouliez que ce soit fini.</li>
</ul>
<p style="text-align: justify;">Peu importent les raisons, le point commun à cette session de mauvais code est toujours une promesse malheureusement vite dite et vite oubliée, <b>“je nettoierai plus tard”.</b></p>
<p style="text-align: justify;">Une fois que notre code marche, on se dit : <b>“</b>un mauvais code qui marche, c'est mieux que rien<b>”</b>, puis on passe à autre chose avec l’idée de <b>nettoyer plus tard</b>, sauf que <b><i>Plus tard est égal à jamais</i></b>.</p>
<p style="text-align: justify;">Seulement voilà, plus les features s’accumulent, plus le mauvais code est présent, et moins votre logiciel est maintenable, ce qui se traduit par une productivité de l'équipe qui ne cesse de diminuer, et qui tend finalement vers zéro. Ce qui nous mène à la nécessité de réécrire le code, proprement cette fois ci, afin d’y voir plus clair</p>
<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/Graph-Clean-Code1.png"><img class="aligncenter size-full wp-image-1906" alt="Graph Clean Code" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/Graph-Clean-Code1.png" width="511" height="222" /></a></p>
<p style="text-align: justify;"><b><i>Clean code </i></b><b>?</b></p>
<p style="text-align: justify;">Vous êtes  bien conscient qu’un mauvais code est un obstacle important, et la seule façon d’aller vite est de garder le code propre.</p>
<p style="text-align: justify;">La question qui se pose est "Comment pouvez-vous écrire du code propre", il n'est pas possible d'essayer d'écrire du code propre si vous ne savez pas ce que cela signifie!</p>

<ul style="text-align: justify;">
	<li>Un code propre est avant tout un code qui doit être agréable à lire.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un mauvais code permet au désordre de grandir! Plus on a des bouts de code mauvais, plus la qualité de notre logiciel tend vers la décadence.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un code propre doit prêter attention aux détails et doit être est focalisé, chaque fonction, chaque classe, chaque objet expose une attitude simple qui reste entièrement indépendante, et non polluée par les détails non nécessaires.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un code propre doit clairement exposer le problème à résoudre et ne doit contenir que ce qui est nécessaire.</li>
</ul>
<ul style="text-align: justify;">
	<li>Il y a  une différence entre le code qui est facile à lire et le code qui est facile à changer, un code propre doit être facile à améliorer par d'autres personnes.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un code non testé n’est pas un code propre, peu importe s'il est lisible et accessible.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un code propre est un code qui a été pris en charge par quelqu'un qui a pris le temps de rester simple et ordonné.</li>
</ul>
<ul style="text-align: justify;">
	<li>Ne contient pas de duplication.</li>
</ul>
<ul style="text-align: justify;">
	<li>Un code propre est aussi un code qui réduit au maximum le nombre d'entités telles que les classes, méthodes, etc.</li>
</ul>
<ul style="text-align: justify;">
	<li><b>Évident, simple et convaincant</b>: quand on lit un code propre, on ne doit dépenser trop d’effort pour le comprendre,</li>
</ul>
<p style="text-align: justify;">Savoir si le code est propre ou mauvais n’est pas suffisant, <b>comment peut-on écrire un code propre ?</b></p>
<p style="text-align: justify;">Plusieurs techniques et bonnes pratiques faciles à adopter existent pour aider à écrire un code propre : nommage explicite de vos fonctions et variables – se passer de commentaires le plus possible, fractionnement de votre code en de multiples petites fonctions et bien d’autres … ce sera le sujet de mes prochains articles.</p>
<p style="text-align: justify;">En attendant n’oubliez jamais, ce sont les programmeurs qui font que le langage paraît simple!</p>
