---
layout: post
title: La qualité de code à tous les niveaux - Round-Table Software Craftsmanship Janvier 2012
date: 2012-02-03 13:21
author: cyrille martraire
comments: true
categories: [abstraction, Agilité, Bonnes pratiques de dév, compte-rendu, craftsmanship, DDD, design, discussion, Evénements, Programmation, qualité, report, round-table, software, TDD, YAGNI]
---
<p style="text-align: justify;">Qu'est-ce que la qualité de code ? Vous vous doutez que l'état de l'art dans notre métier ne permet pas aujourd'hui de donner une réponse claire qui fait l'unanimité !</p>

<div style="text-align: justify;">C'est justement pour échanger sur ces questions que nous nous retrouvons lors des Round-Tables de la communauté Software Craftsmanship Paris, dont voici le compte-rendu de la session de Janvier dernier.  Ce post n'est qu'une tentative de rapporter les échanges de façon organisée, sans autre prétention. La discussion peut aussi continuer dans les commentaires !</div>

&nbsp;

<div style="text-align: justify;">Un mini sondage pour commencer: tout le monde pense qu'il est facile de reconnaitre du mauvais code, en revanche il semble bien plus difficile de reconnaitre du bon code! En l'absence d'expert UX (user experience), "qualité"  se réfère ici à la qualité interne du code, par opposition à la qualité perçue par l'utilisateur.</div>

&nbsp;

<h1 style="text-align: justify;"><strong>"Deliver and forget"</strong></h1>

<div style="text-align: justify;">Une super définition qui nous a bien plu à tous est l'idée qu'un code de qualité est un code qui une fois livré ne fait plus parler de lui: "Deliver and Forget!". Cela comprend l'absence de retour de bugs et la fonctionnalité satisfaite. Cette définition ne concerne que le court-terme, et doit être complétée par "facile à estimer et à retravailler plus tard pour des évolutions".</div>

<div style="text-align: justify;">

Difficile de parler de qualité de code sans rappeler <a href="http://www.jbrains.ca/permalink/the-four-elements-of-simple-design">les 4 règles de "simple design"</a> : un design est simple dans la mesure où, par ordre de priorité <strong>décroissante</strong>, il:
<div>
<ol>
    <li>Passe tous ses tests (et est donc évidemment testé)</li>
    <li>Maximise l'expressivité du code (facile à lire et à comprendre)</li>
    <li>Minimise toute duplication (certains échangent l'ordre des points 2 et 3)</li>
    <li>Contient le moins d'éléments possibles (méthodes, classes, lignes de code)</li>
</ol>
</div>
</div>

<div style="text-align: justify;">Le nombre de lignes de code seul n'est pas un bon critère de jugement. Quand il s'agit de comparer un langage et un autre, la concision est souvent discutée comme un avantage, pourtant la verbosité de nos langages usuels a souvent des vrais avantages (voir <a href="http://techblog.bozho.net/?p=742">Why I Like The Verbosity of Java</a>, voir aussi les commentaires)</div>

<blockquote>
<div>Si 3 mois après j'arrive à relire mon code alors c'est qu'il est pas trop mal.</div></blockquote>

<div style="text-align: justify;">Une technique pour améliorer est de s'arrêter à mi-chemin, puis de laisser un partenaire prendre le relais (ping-pong). Ce qui nous amène évidemment au TDD !</div>

&nbsp;

<h1 style="text-align: justify;"><strong>Qualité et TDD</strong></h1>

<div>
<div style="text-align: justify;">Nous sommes pour la plupart convaincu des avantages du TDD. Cependant on entend une remarque intéressante d'un développeur qui travaille au forfait en solo "pas toujours en TDD, avec TDD surtout pour des parties complexes, parce qu'on vend du code, pas des tests !" Justement, pourquoi on ne vendrait pas aussi les tests ?</div>
<div style="text-align: justify;">Cette remarque oblige aussi à mentionner le post de Martin Fowler sur le sujet que croire que la qualité interne fait perdre du temps est le problème: "the true value of internal quality - that it's the enabler to speed" (voir TradableQualityHypothesis par de Martin Fowler).</div>
<div style="text-align: justify;">C'est l'occasion de rappeler que TDD est très utile même pour des fonctionnalités faciles, quand il s'agit d'élaborer l'API du point de vue du client qui la consommera. Un participant précise : "dans mon équipe le pair programming est utile pour tout le monde, même pour les experts ou les seniors".</div>
<div style="text-align: justify;">
<div>Un sondage délibérément <wbr>simpliste: "préférez-vous un code avec un bon design, ou un code bien couvert par des tests?" Une majorité préfère plutôt un code bien testé, car l'essentiel est de pouvoir intervenir dessus avec moins de danger, et que le fait d'être bien testé rend plus probable que le design ne soit pas trop mauvais. Bien entendu on entre rapidement dans un dilemme circulaire, car un bon design devrait aussi permettre l'ajout facile de tests <em>a posteriori</em>.</wbr></div>
<div>En réponse à ce dernier point, un participant nous rapporte qu'il a fait l'expérience de ressortir un vieux code d'un design correct, et de tenter <em>a posteriori</em> de le rendre testable, en binôme. Il s'avère que cela demande un effort beaucoup plus important qu'imaginé, "c'est super dur, ça nous a demandé un énorme effort pour atteindre seulement 40% de couverture".</div>
</div>
<div style="text-align: justify;">Comment améliorer la qualité du code legacy? La règle du Boy Scout: "laisser le camp plus propre qu'on ne l'a trouvé". Pour le reste c'est un sujet bien trop vaste en lui-même !</div>
&nbsp;
<h1 style="text-align: justify;"><strong>Qualité de design</strong></h1>
<div>La qualité est aussi au niveau du design, qui n'est pas toujours facile à comprendre: la qualité du code c'est aussi de ne pas avoir besoin de lire des centaines de classes et méthodes pour comprendre la structure de l'application. Il s'agit donc là d'exprimer l'intention de design, ce qui peut nécessiter l'ajout de commentaires (patterns par exemple), même si on rappelle que en général les méthodes et classes doivent être compréhensibles par leurs noms seuls, sans besoin de commentaire supplémentaire.</div>
<blockquote>
<div>Utiliser le vocabulaire du client et du métier aide énormément</div></blockquote>
<div style="text-align: justify;">à réduire l'effort de découverte. On pense alors évidemment à l'Ubiquitous Langage de Domain-Driven Design.</div>
<div style="text-align: justify;">
<div>Un bon design implique une bonne séparation de la définition des différentes fonctionnalités constituant l'application. Cette séparation se manifeste par l'absence de mélange de l'implémentation de plusieurs fonctionnalités dans le même module. De même, nous n'avons pas la définition d'une seule fonctionnalité qui est dispersée sur plusieurs modules. Mélange et dispersion donc comme une autre façon de juger la qualité d'un design.</div>
</div>
<div style="text-align: justify;">Enfin en matière d'architecture ou de style, "A Rome, faites comme les romains". Une nette majorité des développeurs présents mettent la priorité à l'homogénéité du style et de l'architecture, devant sa qualité propre. Par exemple, si le style de développement sur un projet existant est d'avoir des POJO anémiques et des comportements regroupés dans une couche de services, alors on doit suivre ce style même si on préfèrerait nettement un style plus objet, avec des objets riches de données et de comportements.</div>
&nbsp;
<h1 style="text-align: justify;"><strong>Qualité d'abstraction et niveaux d'abstraction</strong></h1>
<div style="text-align: justify;">On a tous entendu parler de niveaux d'abstraction, mais c'est quoi vraiment ? En général une abstraction définit une intention, et le niveau en dessous implémente cette intention. Par exemple l'intention de "notifier un utilisateur" peut être implémentée par "envoyer un email". Il est important de ne pas mélanger plusieurs niveaux d'abstractions dans un même scope.</div>
<div style="text-align: justify;">Dans le cas des méthodes, c'est assez simple: si le nom d'une méthode est dans un certain niveau d'abstraction, alors le contenu de la méthode est d'un niveau inférieur. Par exemple la méthode ci-dessous raconte une histoire à un niveau d'abstraction homogène :</div>
<pre>void remindMeetup(...){
   if(isMember(...) &amp;&amp; meetup.isNextDay(...)){
       notifyUser(...);
   }
}</pre>
<div style="text-align: justify;">Alors que cette autre version mélange intention (notifier si membre et si meetup est demain) avec l'implémentation de la notification (envoyer email) :</div>
<div style="text-align: justify;">
<pre>void remindMeetup(...){
   if(isMember(...) &amp;&amp; meetup.isNextDay(...)){
       sendEmail(...);
   }
}</pre>
</div>
<div style="text-align: justify;">Dans le premier cas, notifyUser() n'est qu'un alias sur sendEmail(), spécifiquement pour faire que le code se lise comme une phrase de niveau d'abstraction homogène. Si chaque méthode correspond à un mot, le statement qui appelle ces méthodes correspond alors à une phrase qui doit se lire de façon naturelle et fluide.</div>
<div style="text-align: justify;">Sur cet exemple, on peut critiquer que cette méthode alias est un cas de spéculation (prévoir le cas où on souhaiterait changer de mode de notification), et est donc une complexité inutile tout de suite, auquel cas on crie YAGNI ! D'un autre coté on peut aussi considérer que l'utilisateur veut réellement notifier, et que ce mot correspond au vrai niveau d'intention de l'utilisateur. On rappelle aussi qu'il faut se méfier des spécifications orientées "solutions" (envoyer un email) au lieu de décrire le problème (juste notifier).</div>
<div style="text-align: justify;">En matière d'architecture ou de design, regarder les noms de classes suffit en principe, le contenu des classes est trop détaillé; les noms de classes forment aussi un niveau d'abstraction, le plus haut visible dans le code.</div>
<div>
<div>
&nbsp;
<h1 style="text-align: justify;"><strong>Qualité bas niveau</strong></h1>
<div style="text-align: justify;">Violations Sonar, PMD, Checkstyle: certaines équipes ne sont pas assez matures pour respecter les conventions de codage et d'hygiène minimale. A ce propos : "si on commence à penser en terme de copier-coller, c'est déjà qu'il y a un problème !" A ce niveau par contre on est facilement unanimes dans nos opinions !</div>
</div>
&nbsp;
<h2 style="text-align: justify;">Notes</h2>
<div style="text-align: justify;">La liste approximative des participants est sur la <a href="http://www.meetup.com/paris-software-craftsmanship/events/43676072/">page Meetup du groupe</a>.</div>
<div style="text-align: justify;">Un grand merci à Arolla, sponsor salle et galettes de cette soirée !</div>
</div>
</div>

&nbsp;
&nbsp;
&nbsp;

<p style="text-align: justify;">Quelques liens pour en savoir plus:</p>

<a href="https://fr.wikipedia.org/wiki/Software_craftsmanship" target="_blank">https://fr.wikipedia.org/wiki/Software_craftsmanship</a>

<a href="http://manifesto.softwarecraftsmanship.org/" target="_blank">http://manifesto.softwarecraftsmanship.org/</a>

<a href="http://www.meetup.com/fr/paris-software-craftsmanship/" target="_blank">http://www.meetup.com/fr/paris-software-craftsmanship/</a>

<a href="http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862" target="_blank">http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862</a>
