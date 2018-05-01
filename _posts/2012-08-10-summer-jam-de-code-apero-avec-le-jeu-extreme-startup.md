---
layout: post
title: Summer Jam de Code & apéro, avec le jeu "Extreme Startup"
date: 2012-08-10 15:48
author: cyrille martraire
comments: true
categories: [Bonnes pratiques de dév, coding, craftsmanship, Evénements, Extreme, Extreme Startup, game, geek, kata, Programmation, regex, TDD, web, workshop]
---
<p style="text-align: justify;">Extreme Startup est une compétition d'écriture de code en direct où des équipes s'affrontent en code face à un serveur qui représente le marché, le tout sous la pression des requêtes qui ne cessent d'atteindre votre serveur.</p>

<h1 style="text-align: justify;">Extreme Startup, un truc de geeks ?</h1>
<p style="text-align: justify;">Extreme Startup est totalement geek, et même plus. Vos outils pour participer : le code dans le langage de votre choix, votre serveur web local, vos frameworks ou outillages divers, vos techniques, TDD ou "à l'arrache", et toutes vos pratiques, agiles ou non.</p>

<div class="mceTemp" style="text-align: justify;"><dl id="attachment_1136" class="wp-caption alignnone" style="width: 310px;"><dt class="wp-caption-dt"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code1.jpg"><img class="size-medium wp-image-1136" title="summer_jam_de_code1" src="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code1-300x225.jpg" alt="" width="300" height="225" /></a></dt><dd class="wp-caption-dd">La mise en place initiale</dd></dl></div>
<p style="text-align: justify;">Un peu comme certains jeux de simulation d'environnement concurrentiel que l'on rencontre parfois en école de commerce, chaque équipe dans le jeu représente une startup qui veut être profitable en répondant au plus vite aux besoins du marché. Et bien entendu, le marché ne prévient pas lorsqu'il a un nouveau besoin, il faut donc être à l'écoute, et réagir vite.</p>
<p style="text-align: justify;">Vous commencez donc avec un serveur web local minimal, qui est spammé par le serveur central du jeu qui envoie les requêtes avec les questions à répondre.</p>
<p style="text-align: justify;">Ce jeu a été crée par <a href="http://twitter.com/#!/rchatley">@rchatley</a> and <a href="http://twitter.com/#!/mattwynne">@mattwynne</a>, en Ruby, et le code est disponible sur <a href="https://github.com/jhannes/extreme_startup">Github</a>. Et lors de notre Jam de Code, c'est Sam (<a href="https://twitter.com/samklr/" target="_blank">@samklr</a>) qui s'est occupé d'amener le serveur de jeu, de nous expliquer les règles et de nous dépanner le cas échéant. Merci Sam!</p>

<h1 style="text-align: justify;">Besoin de super-pouvoirs ?</h1>
<p style="text-align: justify;">A première vue, le jeu peut sembler intimidant. Construire une startup web en quelques minutes et être aussitôt compétitif n'est pas si évident. Est-il déjà possible de mettre en place les environnements sans perdre une heure ? Le Wifi peut-il survivre aux sollicitations de tous les téléchargements Maven en parallèle ?</p>
<p style="text-align: justify;">Besoin d'avoir des super-pouvoirs pour jouer ? Non, sauf des super-pouvoirs en expressions régulières ! Ah les regex... Je dois vous avouer que j'aime pas ça, et leur puissance coûte très cher en lisibilité, d'ailleurs &lt;troll&gt;le succès de PERL vient surtout du fait que c'est un <a href="http://en.wikipedia.org/wiki/Write-only_language" target="_blank">Write-Only language</a>...&lt;/troll&gt;.</p>
<p style="text-align: justify;">Même prévenu, et même avec quelques anti-sèches au tableau, le jeu exige au départ une bonne familiarité avec des bases de regex, et aussi avec l'API pour les manipuler (Pattern et Matcher dans le cas de Java). Par exemple il faut savoir, sous peine d'une bonne dose de frustration, que la capture de valeurs est indexée de 1 à n, avec l'index 0 en valeur magique qui retourne toute la chaine... Pas le meilleur exemple d'une API intuitive, ou alors je n'ai pas encore assez de barbe pour que ça soit intuitif pour moi.</p>

<h1 style="text-align: justify;">TDD ou hack à l'arrache ?</h1>
<p style="text-align: justify;">On a tous constitué des binômes, avec une machine pour deux. Ensuite la grosse question était le choix de la manière de travailler, sachant que l’exercice n'est pas très long (2h environ en principe), faut-il choisir TDD, Clean Code, ou  une bidouille délibérément non maintenable ? Vous avez certainement vos réponses en tête, mais l'intérêt du jeu est justement de mettre à l'épreuve nos préconceptions.</p>
<p style="text-align: justify;">Dans le cas de notre binôme, on a choisi une sorte de TDD peu strict, avec des tests avant et une implémentation dans le test case, copiée ensuite (je sais c'est bête mais on veut vraiment livrer sans perdre une seconde) dans le code sur serveur. La discipline n'a pas été suffisante, avec des implémentations qui semblaient faciles réalisées directement sur le serveur, sans test... Moralité à mon sens :</p>

<blockquote>Couper les coins ne paie pas !</blockquote>
<p style="text-align: justify;">On se fait mordre ensuite, même sur du code super facile : un copier-coller de regex oublié, une erreur de formatage non vue, un cas limite négligé... autant d'erreurs découvertes trop tard "en production", et qui font perdre des points pendant tout ce temps. J'estime que cela nous a coûté plus cher que le temps à écrire le test.</p>
<p style="text-align: justify;">D'un autre coté, le "marché" à certains moments pénalisait davantage le fait de ne pas répondre plutôt que de se tromper ou de jeter une exception. Être rapide "à tout prix" a parfois du sens.</p>

<div class="mceTemp" style="text-align: justify;"><dl id="attachment_1144" class="wp-caption alignnone" style="width: 310px;"><dt class="wp-caption-dt"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code2.jpg"><img class="size-medium wp-image-1144" title="summer_jam_de_code2" src="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code2-300x167.jpg" alt="" width="300" height="167" /></a></dt><dd class="wp-caption-dd">Les binômes sont sous pression, le jeu augmente sans cesse d'intensité</dd></dl></div>
<p style="text-align: justify;">Nous avons très peu refactoré, et mon binôme souhaitait en faire plus. Je n'ai pas senti autant la pression du code à refactorer, l'important étant que notre code était bien nommé depuis le début, sans quoi on aurait rapidement été dépassés et perdus...</p>
<p style="text-align: justify;">J'ai aussi parfois le sentiment qu'avec la pression du jeu on aurait pu paralléliser un petit peu certaines tâches, au-delà d'aller chercher sur Wikipédia les réponses à certaines questions, comme la couleur d'une banane (allez je vous donne un indice, c'est "Yellow").</p>
<p style="text-align: justify;">J'ai été rassuré sur la qualité de collaboration de notre binôme lorsque que Delphine nous a dit qu'on avait :</p>

<blockquote>une coordination impeccable, l'un code pendant que l'autre sert à boire à tout le monde !</blockquote>
<h1 style="text-align: justify;">Tous les moyens sont-ils permis ?</h1>
<p style="text-align: justify;">J'ignorais que dans ce jeu on pouvait aussi "jouer" par des attaques de type DoS (Denial Of Service), ou par des leurres en envoyant des questions parasites aux autres équipes, par exemple "what is 0 or 1", ou "si les violettes sont violettes, comment sont les roses" (exemples réels pendant la soirée). C'est totalement déloyal, mais assez drôle, et bon pour l'ambiance !</p>
<p style="text-align: justify;">Il faut dire que l'IP de chaque équipe est affichée sur l'écran des scores des équipes, ce qui pousse un peu au crime. Chaque équipe est aussi libre de vérifier l'IP des requêtes, comme dans la vraie vie...</p>
<p style="text-align: justify;">j'ai aussi observé que le fait de gagner ou perdre change aussi un peu la façon de penser ; est-ce une bonne chose ou encore un biais cognitif à apprendre à maîtriser ?</p>

<h1 style="text-align: justify;">Une métaphore Lean Startup</h1>
<p style="text-align: justify;">Le jeu Extreme Startup est donc une métaphore technique de la situation chez les startups, avec le dilemme entre répondre vite sans assurer ses arrières en compromettant les développements futurs, ou investir proprement pour le long terme tout en perdant un avantage concurrentiel décisif lors des premières phases.</p>
<p style="text-align: justify;">Le jeu tente de simuler tout ça avec différents comportements, dont des comportements d'effet de mode, où une équipe est d'un coup beaucoup plus sollicitée sans être la meilleure. On a d'ailleurs bénéficié de cet effet pour prendre la première place et gagner à l'issue du jeu, alors que d'autres équipes avaient un produit de qualité objectivement supérieure. Comme dans la vraie vie !</p>
<p style="text-align: justify;">La métaphore du jeu reste cependant technique, car une fois les regex sous contrôle, les questions admettent toutes des réponses exactes, qui ne font guère appel à la créativité, contrairement aux problématiques du monde réel.</p>

<h1 style="text-align: justify;">Et l'apéro alors ?</h1>
<p style="text-align: justify;">Comme prévu la soirée s'est terminée en terrasse autour d'un apéro au café L'Imprimerie tout proche. Et devinez de quoi on a parlé jusqu'aux derniers métros ?</p>

<div class="mceTemp" style="text-align: justify;"><dl id="attachment_1143" class="wp-caption alignnone" style="width: 235px;"><dt class="wp-caption-dt"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code_apero.jpg"><img class="size-medium wp-image-1143" title="summer_jam_de_code_apero" src="http://www.arolla.fr/blog/wp-content/uploads/2012/08/summer_jam_de_code_apero-225x300.jpg" alt="" width="225" height="300" /></a></dt><dd class="wp-caption-dd">Apéro en terrasse entre geeks, ça parle de trucs de geek</dd></dl></div>
