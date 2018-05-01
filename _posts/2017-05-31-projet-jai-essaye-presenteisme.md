---
layout: post
title: Le projet où j'ai essayé le présentéisme
date: 2017-05-31 10:33
author: hadrien mens-pellen
comments: true
categories: [anti pattern, bien-être au travail, Bonnes pratiques de dév, clean code, craftsmanship, présentéisme, travail]
---
<img title="" src="https://i.imgflip.com/1osr8v.jpg" alt="Workaholics, presentism for the win !" />

<strong>Spoiler alert</strong> : Le culte du temps de travail, c’est mal, la qualité de code, c’est bien

Du simple acte de présence à l’addiction au travail, le présentéisme peut prendre de nombreuses formes. En France plus qu’ailleurs, il est bien vu dans de nombreuses entreprises. La plupart d’entre vous a d’ailleurs déjà dû entendre la fameuse pique “Tu as pris ton après-midi ?” lancée à quelqu’un qui quitte le bureau à 17h30. LOL !

Dans une mes précédentes entreprises, on appelait cela “l’implication”. Du développeur tout juste intégré aux managers du plus haut niveau, c’était une des valeurs les plus importantes. Le nombre d’heures travaillées par jour pouvait faire la différence au moment d'être augmenté (ou non).

C’est comme ça qu’on gérait l'imprévisibilité. Pour livrer à l’heure, il fallait rester tard. Mais attention! On se gardait bien de prononcer les mots “heures supplémentaires”!

Après plusieurs missions sans être augmenté, car pas suffisamment “impliqué”, je me suis dit qu’après tout :

<ul>
    <li>c'était une des plus grandes entreprises d’informatique de France, s’ils réussissaient, leur modèle n’était peut-être pas si mauvais</li>
    <li>tous les gens que je respectais dans cette société fonctionnaient de cette manière</li>
    <li>si c’est ce qu’il fallait pour réussir dans mon métier de développeur, alors j'allais au moins essayer.</li>
</ul>

<h2 id="une-success-story">Une success story</h2>

J’ai profité du fait d’être propulsé lead developer dans une équipe pour me lancer. En théorie, j’avais des horaires fixes : 7h30 de travail par jour. Mes collègues architectes en faisaient plus : 9h30 - 19h avec une courte pause déjeuner. J’ai commencé à les imiter. L’effet a été immédiat : mes managers ont été contents de moi pour la première fois depuis looooongtemps !

Avec l’arrivée de la première livraison et le rush des deux dernières semaines, je suis rapidement passé à du 9h-21h sans vraiment prendre de pause à midi pour tenir les délais. La dernière semaine avant la livraison on était même plus sur du 9h-23h. J’étais épuisé, mais j’avais vraiment l’impression de faire partie de l’équipe. Tout le monde travaillait dur et longtemps. Quand je partais à 23h après avoir livré en recette, je savais que le chef de projet et le directeur de projet allaient recetter de 1h à 4/5h du matin. Et le client était livré en temps et en heure. Après les rushs, j’avais parfois une ou deux journées de repos offertes et on repartait sur un rythme moins violent, mais ça revenait très vite.

Un an après, j’ai reçu des augmentations, les premières en 2 ans, et j'ai été mis sur la liste courte pour une promotion. Les managers de toute la division disaient du bien de moi et de ce projet. Victoire !

<img title="" src="https://i.imgflip.com/1orb1h.jpg" alt="Macron Superstar" />.

<h2 id="le-revers-de-la-médaille">Le revers de la médaille</h2>

J’ai fait plus de 400 heures supplémentaires non rémunérées, ce qui fait environ 50 jours de travail ou 2 mois et demi. Il va sans dire que je n’avais plus de vie personnelle, le weekend n'étant plus fait que pour dormir !

Les délais nous ont poussés à ne pas faire de revues de code, de binômage, de tests automatiques. Nous n’avons pas détecté l’apparition de graves failles de sécurité. Le code est devenu incompréhensible et impossible à maintenir, même par l’équipe. En conséquence, il est arrivé que le temps de correction des régressions soit 3 fois supérieur au temps de développement.

Le client se plaignait du coût des évolutions et a fini par geler le projet. Ce ne fut pas le meilleur investissement pour mon employeur...

Enfin, je n’ai pas beaucoup progressé en développement et je ne peux pas dire que je sois fier de ce projet.

<h2 id="travailler-moins-pour-gagner-plus">Travailler moins pour gagner plus</h2>

Mais j’ai appris quelques choses et pris des décisions pour mes futurs projets. La pierre angulaire ? Il faut travailler moins.

<img title="" src="http://www.awesomeinventions.com/wp-content/uploads/2016/02/Efficiency.jpg" alt="Laziness is a synonym of efficiency" />

<h3 id="1-au-bout-dune-certaine-durée-de-travail-on-arrête-dêtre-productif">1. Au bout d’une certaine durée de travail, on cesse d’être productif.</h3>

Je l’ai constaté lors de mes plus longues journées, je n’arrivais plus à me concentrer et j’avais du mal à trouver les solutions aux problèmes. Pire, parfois mon travail dans ces moments-là créait des régressions que je devais corriger le lendemain. En 2004, une étude américaine, réalisé par Hemp, conclut qu’après 8h de travail, nous devenons contre-productifs. En 2014, Midori Consulting publie un <a href="http://www.midori-consulting.com/?page_id=3487" target="_blank">baromètre</a>* affirmant que le présentéisme coûterait même plus cher aux entreprises que l'absentéisme.   <img src="http://www.arolla.fr/blog/wp-content/uploads/2017/05/pyramide-de-perturbation-du-travail.jpg" alt="pyramide-midori" width="884" height="462" class="aligncenter size-full wp-image-4583" />

De plus, nous avons tous un rythme de croisière au travail. Se pousser au-delà de ce rythme fait baisser notre endurance. Les projets sont des marathons de plusieurs mois à plusieurs années, pas des sprints. Pousser son rythme et réduire son endurance, c’est se précipiter vers le burn out.

<h3 id="2-plus-de-pauses-plus-de-créativité">2. Plus de pauses, plus de créativité</h3>

Le développement est un travail créatif. Nos capacités à résoudre nos problèmes de développement dépendent de notre habilité à associer nos problèmes à des solutions “classiques” (des patterns). Dans le pire des cas, nous devons trouver une nouvelle solution.

La créativité a besoin de temps. La situation a déjà dû vous arriver, vous êtes coincés en fin de journée sur un sujet et en arrivant le matin, la solution vous paraît soudain évidente. Prendre du recul, du temps, penser à autre chose sont des éléments essentiels à un code de qualité.

<h3 id="3-pas-de-qualité-pas-dévolutions">3. Pas de qualité, pas d’évolutions</h3>

L’arrêt des lots d’évolution sur ce projet et mes expériences sur d’autres projets difficiles à maintenir m’ont montré que sans qualité de code, il n'y a pas d’évolution possible. La mauvaise qualité du code (et souvent de la documentation) augmentent le temps nécessaire pour le faire évoluer sans bugs de manière exponentielle.

<h3 id="4-on-peut-satisfaire-tout-le-monde-avec-plus-de-qualité">4. On peut satisfaire tout le monde avec plus de qualité</h3>

En simplifiant grossièrement, pour réussir un projet, il faut satisfaire le client, le management et les développeurs :

<ul>
    <li>Le client veut des fonctionnalités le plus vite possible et pour longtemps, un logiciel pérenne. Il pousse donc en général pour réduire les délais et payer moins cher.</li>
    <li>Le management veut faire le plus de marge possible. Pour ce faire, il veut faire plaisir au client afin de continuer à vendre tout en minimisant les coûts.</li>
    <li>Le développeur veut en général produire du code de qualité, dont il peut être fier, en souffrant moins de la dette technique et en conservant un certain équilibre avec sa vie personnelle.</li>
</ul>

Se focaliser sur la qualité de code permettrait de satisfaire tout le monde. En effet, un code de qualité, maintenable et bien documenté permet d’ajouter des fonctionnalités à un rythme bien plus élevé dans le temps. Dans mon cas, le logiciel a vécu pendant un an et demi avant de geler. Comme certains requins, un logiciel qui reste statique est condamné. Si nous avions pu ajouter des fonctionnalités sans ce risque élevé de régressions et des coûts prohibitifs, peut-être que ce projet aurait pu vivre plus longtemps. Dans ce cas, le client aurait eu son logiciel pérenne, le management en aurait tiré des bénéfices sur le long terme (sans compter l’effet bénéfique sur la réputation de l’entreprise) et les développeurs auraient été plus heureux.

<hr />

Ces conclusions paraissent évidentes à de nombreux développeurs. Pourtant, même les plus convaincus ne les appliquent généralement pas.

Parfois je doute encore aussi. Je suis souvent tenté de faire plaisir au client en livrant vite. Je sacrifie la qualité ou mon temps personnel. A chaque fois que je l’ai fait, ça m’a créé des problèmes (régressions, élévation du coût de maintenance, etc.). D’autres fois, je pars dans l’autre direction, je deviens un nazi de la qualité de code et je tourne en rond. Un jour, je trouverai l’équilibre (binômage to the rescue).

<img title="" src="https://img4.hostingpics.net/pics/968169sapologieequilibre.jpg" alt="Equilibre, Sapologie" />

Pour conclure, je vais paraphraser <em><a href="http://blog.cleancoder.com/" target="_blank">Clean Coder</a></em> :

<ul>
    <li>Il ne faut pas négocier sur la qualité ou les tests,</li>
    <li>il faut négocier les fonctionnalités à coder dans le temps imparti.</li>

 Les heures supplémentaires sont à considérer :
<ul>
    <li>en dernier recours</li>
    <li>à un volume raisonnable (une heure ou deux par jour maximum)</li>
    <li>pendant une durée prédéterminée (une ou deux semaines maximum)</li>
    <li>avec une autre solution en cas d’échec</li>
</ul>
</li>
</ul>

Et vous, comment gérez-vous la pression sur les délais de livraison ?

<ul>
<li><a href="http://www.midori-consulting.com/?page_id=3487" target="_blank">1er Baromètre Midori du Présentéisme au Travail</a></li>
</ul>
