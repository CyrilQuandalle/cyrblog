---
layout: post
title: "Dataviz & BigData : mythes & réalités [TechDays 2014]"
date: 2014-02-13 10:06
author: cyrille martraire
comments: true
categories: [Actu, graphe, Programmation, TechDays, visualization]
---
<p style="text-align: justify;">Cette session du premier jour des TechDays 2014 était vraiment intéressante et pédagogique. Elle était présentée par <a href="https://twitter.com/jdfaviz" target="_blank">Jean-Daniel Fekete</a>, <a href="http://en.wikipedia.org/wiki/Jean-Daniel_Fekete" target="_blank">directeur de recherche</a> à Inria Saclay-Ile de France et spécialiste de "l'analyse et la visualisation d'informations volumineuses et complexes."</p>

<h1 style="text-align: justify;">Dataviz ou Infoviz ?</h1>

<p style="text-align: justify;">Tout d'abord, quelle différence entre Dataviz, Infographie et Infoviz ?</p>

<ul style="text-align: justify;">
    <li><a href="http://en.wikipedia.org/wiki/Data_visualization" target="_blank">Dataviz</a> (Data Visualization) : c'est pour afficher des données (ok)</li>
    <li><a href="http://en.wikipedia.org/wiki/Information_graphics" target="_blank">Infographie</a> : c'est pour la presse, c'est graphique</li>
    <li><a href="http://en.wikipedia.org/wiki/Information_visualization" target="_blank">Infoviz</a> (Information Visualization) : c'est interactif pour interagir avec les données</li>
</ul>

<p style="text-align: justify;">Parmi toutes ces visualisations, on trouve beaucoup de choses, avec une grande variété de styles, et pour beaucoup elles sont très belles. Mais toutes ces visualisations sont-elles vraiment efficaces ?</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/02/dataviz.jpg"><img class="alignnone  wp-image-2205" alt="dataviz" src="http://www.arolla.fr/blog/wp-content/uploads/2014/02/dataviz-1024x768.jpg" width="728" height="546" /></a></p>

<p style="text-align: justify;">Et pour commencer, un premier principe fondamental : tout le monde sait comprendre les visualisations <strong>congruentes.</strong> Congruentes ? Ça mérite une explication !</p>

<h1 style="text-align: justify;">Une histoire de congruence</h1>

<p style="text-align: justify;">La congruence, c'est lorsqu'une visualisation se lit de la même façon que la question qu'on se pose :</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/02/congruenteviz.jpg"><img class="alignnone size-medium wp-image-2202" alt="congruenteviz" src="http://www.arolla.fr/blog/wp-content/uploads/2014/02/congruenteviz-300x225.jpg" width="300" height="225" /></a></p>

<ul style="text-align: justify;">
    <li>Qui est le plus grand ou le plus petit ? C'est facile à voir sur un bargraphe. Chercher le plus grand dans le problème appelle le même comportement visuel de chercher la barre la plus grande dans le graphique.</li>
    <li>Quelle est la répartition d'un budget total ? Un camembert est parfaitement approprié.</li>
    <li>Évolution d'une tendance dans le temps ? Line chart conviendra.</li>
    <li>Étudier les connections entre des gens ? Un graphe de réseau avec des nœuds et des liens est une représentation assez naturelle.</li>
</ul>

<p style="text-align: justify;">Passons désormais aux Treemaps : on peut rapidement les comprendre. Mais si on regarde les Treemaps parues dans Le Monde et dans Libération, on constate qu'<a href="http://data.blog.lemonde.fr/2012/10/22/un-seul-budget-plusieurs-treemaps/" target="_blank">elles diffèrent nettement</a>. Elles proviennent en effet toutes d'outils de visualisation interactive, où l'utilisateur a la liberté d'effectuer certains choix qui bien entendu influencent le résultat.</p>

<blockquote>À ce stade, il est illusoire d'imaginer qu'un simple dessin puisse tout dire, c'est bien plus compliqué les données ! Si on n'interagit pas, on ne peut pas bien comprendre les données.</blockquote>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/02/scatterviz.png"><img class="alignnone size-medium wp-image-2203" alt="scatterviz" src="http://www.arolla.fr/blog/wp-content/uploads/2014/02/scatterviz-300x300.png" width="300" height="300" /></a></p>

<p style="text-align: justify;">D'autres visualisations comme le scatter plot (nuage de points) demandent un apprentissage. Pour ceux d'entre vous rompus à des études supérieures, c'est familier et il est parfois difficile d'imaginer une difficulté de lecture. Pour les autres, c'est pas bien long à comprendre, 2mn ou 10mn suffisent, mais ça suffira pour qu'un lecteur non éduqué ignore la page qui porte ce genre de visualisation.</p>

<blockquote>On remarque alors que ce qu'on peut comprendre spontanément est bien mais pas très efficace, tandis que pour être efficace alors il faut apprendre un minimum.</blockquote>

<h1 style="text-align: justify;">Visualiser des grosses données ?</h1>

<p style="text-align: justify;">Prenons alors l'exemple du choix d'un appareil photo parmi des centaines de modèles et en observant 12 critères. Jean-Daniel présente alors une interfaces "à facettes" pour confronter visuellement 2 dimensions à la fois, par exemple le prix et la résolution effective, et ce successivement par sélections imbriquées. Une animation de rotation visuelle en 3D montre le changement d'axe, même si en fait les dimensions alternent parmi 12, bien au-delà des 3 dimensions de notre expérience familière.</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/02/infoviz12.jpg"><img class="alignnone  wp-image-2204" alt="infoviz12" src="http://www.arolla.fr/blog/wp-content/uploads/2014/02/infoviz12-1024x768.jpg" width="728" height="546" /></a></p>

<p style="text-align: justify;">Certaines visualisations sont tellement encombrées qu'elles en deviennent inexploitables. Par exemple l'application inmaps sur Linkedin affiche l'intégralité de votre réseau LinkedIn. C'est très beau mais si vous avez 500 connections alors c'est très poilu. Sans surprise, vous êtes au centre, car tous les contacts affichés ont par construction une connexion avec vous. La visualisation est alors encombrée de 500 liens qui n'apportent aucune information utile. Cela dit, les masquer ne suffirait sans doute pas à rendre la visualisation suffisamment claire. Aujourd'hui, ce service n'existe plus.</p>

<p style="text-align: justify;">Dans le cas de grands volumes de données, Dataviz et <a href="http://en.wikipedia.org/wiki/Machine_learning" target="_blank">Machine Learning</a> font bon ménage. L'idée est d'alterner entre analyse des données par Machine Learning et Visualisation, pour gagner à mieux comprendre les données et ainsi découvrir comment les présenter efficacement. Cela pourra éventuellement nécessiter des omissions ou autres partis pris.</p>

<p style="text-align: justify;">On aura compris dans que le principal écueil en Dataviz est la méconnaissance des règles essentielles de visualisation. À défaut, votre visualisation sera certes très belle mais probablement incompréhensible.</p>

<h1 style="text-align: justify;">Pour aller plus loin</h1>

<p style="text-align: justify;">Techniquement il n'y a pas de difficulté particulière, avec des librairies disponibles dans la plupart des langages, Python, Java, et désormais la fameuse librairie <a href="http://d3js.org/" target="_blank">d3</a> en Javascript qui offre l'avantage de pouvoir s'inviter dans tous les navigateurs de votre audience.</p>

<p style="text-align: justify;">Enfin à côté de la visualisation on parle aussi d'oralisation pour présenter de données sous forme auditive. L'inconvénient est une bande passante très réduite comparée aux canaux visuels, mais avec l'avantage pour le sujet de pouvoir faire abstraction du contenu auditif.</p>

<p style="text-align: justify;">A noter aussi qu'après des années sur le continent américain la conférence de l'<a href="http://ieeevis.org/" target="_blank">EEE VIS 2014</a> arrive à Paris pour son édition 2014. Ce sera en novembre, avec la présence de tous les spécialistes mondiaux du domaine. À vos agendas !</p>
