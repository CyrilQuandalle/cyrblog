---
layout: post
title: "La programmation fonctionnelle pour les développeurs objets : Revue du livre de Dean Wampler"
date: 2011-11-20 09:27
author: houssam fakih
comments: true
categories: [Fonctionnel, fonctionnel, immutabilité, objet, Programmation]
---
<h1><strong>Introduction</strong></h1>

<p style="text-align: justify;">Je vous présente dans ce billet le livre de Dean Wampler qui introduit la programmation fonctionnelle pour les développeurs objets.</p>

<p style="text-align: justify;">Je vais commencer tout d'abord par vous présenter comment j'ai découvert ce livre ainsi que la programmation fonctionnelle. Le premier langage fonctionnel que j'ai connu est : O'Caml.  Cela remonte aux années 2004 et 2005. C'est le langage que j'ai utilisé pour animer les travaux pratiques d'un cours sur la programmation pour des étudiants non informaticiens dans le cadre d'une formation continue diplômante à l'Ecole des Mines de Douai. En mai 2011, j'ai eu l'occasion de participer à un Aroll@fterwork animé par Cyrille Martraire qui nous a parlé d' "Une touche de programmation fonctionnelle dans votre langage objet quotidien".  Les concepts que j'ai découverts dans ce workshop m'ont motivé pour suivre la formation organisée par /ut7 et animée par Brian Marick pendant son passage à Paris en septembre 2011. Pendant cette formation, Brian nous a offert une version du livre de Dean Wampler. A la lecture de ce livre, j'ai appris beaucoup de concepts simples que je compte utiliser dans mon travail de développement quotidien et que je souhaite partager avec vous dans ce billet.</p>

<p style="text-align: justify;">Le livre de Dean est concis et précis. Il est composé d'une soixantaine de pages qui explique les concepts de base de la programmation fonctionnelle et les apports que pourrait apporter l'intégration de ces concepts dans le monde objet et plus particulièrement dans le langage Java. Pour chaque concept introduit, Dean fournit une implémentation concrète du code écrit en Java. A la fin de chaque chapitre il y a des énoncés d'exercices qui viennent compléter les sujets abordés dans les différents chapitres. Dean a comme objectif de donner aux développeurs objet une nouvelle boîte à outils qui leur permet de faire face aux challenges d'aujourd'hui :</p>

<blockquote>"I’m trying to give you a bigger toolbox and a broadend perspective, so you can make more informed design choices and maybe refersh you enthusiasm for the art and the science of software development."</blockquote>

<p style="text-align: justify;">Je note que pour se lancer dans la programmation fonctionnelle, il n'y a pas besoin d’avoir des bases solides en mathématiques.</p>

<p style="text-align: justify;">Le reste de ce billet est structuré comme suit : le paragraphe suivant vous présente le contenu du livre. Je résume ensuite ce que j'ai retenu du contenu des trois premiers chapitres avant de conclure ce billet et vous donner des liens vers les pages web de l'auteur et du livre.</p>

<h1 style="text-align: justify;"><strong>Contenu du livre</strong></h1>

<p style="text-align: justify;">Le livre est structuré en six chapitres :</p>

<ul style="text-align: justify;">
    <li>le chapitre 1 explique ce que pourrait apporter l'intégration des concepts de la programmation fonctionnelle dans le monde de la programmation par objets,</li>
    <li>les chapitres 2 et 3  introduisent les principes de base de la programmation fonctionnelle, ses structures de données persistantes ainsi que les algorithmes utilisés pour les manipuler. Ces structures seront utiles pour expliquer la gestion de la concurrence,</li>
    <li>le chapitre 4 est consacré à la gestion de la concurrence. L'auteur explique le modèle d'acteurs ainsi que "Software Transactional Memory",</li>
    <li>les chapitres 5 et 6 revisitent les concepts de l'objet et montre comment on peut faire mieux en utilisant les concepts de la programmation fonctionnelle.</li>
</ul>

<h1 style="text-align: justify;"><strong>Chapitre 1</strong></h1>

<p style="text-align: justify;">Dean parle dans ce premier chapitre des problématiques dans le monde objet où la programmation fonctionnelle pourrait apporter des solutions tout en préservant la modularité et sans transgresser les principes de la programmation par objets.</p>

<p style="text-align: justify;">La caractéristique principale de la programmation fonctionnelle est qu'elle garde l'état des entités immutables. Cette immutabilité a l'avantage de faciliter l'écriture des programmes concurrents qui deviennent de plus en plus présents dans les applications d'aujourd'hui notamment avec les processeurs multi-coeurs omniprésents dans les différents appareils communicants sur le marché. Une autre caractéristique de la programmation fonctionnelle est que les fonctions n'ont pas d'effet de bord. Cela les rend indépendants du contexte de tout autre objet et facilite leur réutilisation.</p>

<p style="text-align: justify;">Une autre caractéristique de la programmation fonctionnelle concerne la gestion des données. Le volume de données devient de plus en plus important et ce volume, dans des applications à grande échelle, peut atteindre des terabytes pour les nouvelles données à traiter par jour et petabytes pour les données accumulées à analyser.  La gestion actuelle dans le monde des objets repose essentiellement sur des frameworks de type ORM qui vont définir une couche d'abstraction entre les objets, d'une part, et les données relationnelles dans la base, d'autre part. Cela permet sans doute de séparer les couches l'une de l'autre, éviter la duplication de code en utilisant les mêmes objets pour accéder aux données et améliorer la maintenance. Par contre, il y a une surcharge due à la création des objets. Avec la programmation fonctionnelle, l'auteur promet de travailler d'une manière directe sur les collections de données sans passer par le modèle objet. Ceci a l'avantage d'éviter la surcharge que l'objet peut induire surtout dans le cas des applications à grande échelle. Mais l'utilisation des collections et des principes de la programmation fonctionnelle ne doit pas transgresser les principes du monde objet, surtout la réutilisabilité.</p>

<p style="text-align: justify;">L'auteur évoque les promesses qui ont été faites par le monde objet dans les années 1980 où l'objet promettait des composants sur étagère ("components off the shelf"- COTS) ce qui permet de construire les applications par assemblage des composants entre eux ce qui réduit le coût et simplifie les développements. Il est évident aujourd'hui que cette promesse n'a pas été tenue. Les frameworks largement réutilisés comme Spring, Apache commons, et Eclipse Plugin API définissent une sorte de standards ou d'APIs que le développeur est obligé de suivre mais une grande partie de son application, notamment la partie métier, reste à développer.</p>

<p style="text-align: justify;">Dean explique qu'un concept métier, comme la notion de client par exemple, est modélisé différemment d'une application à une autre. De ce fait, ce concept est difficilement réutilisable. Dean explique cela par un problème de choix de niveau d'abstraction. L'objet représente souvent un concept métier et on raisonne en considérant les services métiers fournis et requis de ce concept. Dean considère ce niveau d'abstraction un peu trop élevé. Afin de faciliter la réutilisation, il voit l'objet comme une agrégation de données qui pourrait être définie en se basant sur des abstractions /entités composées des valeurs primitives et des types de collections fonctionnelles comme : List, Map, Set, etc. ainsi que quelques concepts basiques spécifiques au modèle métier comme par exemple la notion Money dans le domaine des finances. Ainsi, n’importe quel objet quelle que soit sa complexité peut être décomposé en des valeurs primitives et en collections contenant ces valeurs primitives et d’autres collections. Dean considère ce niveau d'abstraction meilleur pour faciliter la réutilisation.</p>

<h1 style="text-align: justify;"><strong>Chapitre 2</strong></h1>

<p style="text-align: justify;">Le chapitre 2 parle brièvement de l'historique de la programmation fonctionnelle initialement conçue par des mathématiciens dans les années 1930 et qui se base sur un formalisme Lambda Calculus pour définir et appliquer les fonctions. Un traitement informatique est donc défini comme une combinaison de fonctions suivant une logique combinatoire. La "théorie des catégories" offre la possibilité pour structurer les traitements informatiques d’une telle manière que les effets de bord comme les IO soient proprement séparés du reste du code. Ce dernier ne devrait pas avoir d’effets de bord.</p>

<p style="text-align: justify;">Le chapitre explique ensuite d'une manière détaillée les principes de base de la programmation fonctionnelle :</p>

<ul style="text-align: justify;">
    <li>éviter les status mutables</li>
    <li>définir des fonctions comme des entités de première classe</li>
    <li>les fonctions antonymes et Closures</li>
    <li>les fonctions d'ordre supérieur (fonctions qui prennent d'autres fonctions en paramètres)</li>
    <li>les fonctions sans effets de bord</li>
    <li>récursion</li>
    <li>évaluation "lazy" vs "eager"</li>
    <li>style déclaratif vs impératif</li>
</ul>

<p style="text-align: justify;">Pour chaque principe, l'auteur étudie s'il pourrait être appliqué dans le langage Java. Si nous prenons par exemple la définition des variables immutables, l'utilisation du mot clé final permet de déclarer des variables Java immutables. Cela est valable dans le cas de variables primitives ou d'objets simples mais ne l'est plus si la variable déclarée est de type collection. Les tierces parties ayant accès à la collection ne peuvent pas la modifier elle-même mais elles ont toujours la possibilité de modifier ses éléments.</p>

<p style="text-align: justify;">En ce qui concerne la définition des fonctions en tant qu'entités de première classe, le langage Java définit des méthodes mais pas de fonctions. Les méthodes ne sont pas par contre déclarées comme des entités de première classes dans Java. Afin de définir des fonctions anonymes dans le langage Java, les développeurs font recours aux classes anonymes dites : "inner" qui sont souvent des classes avec une seule méthode, ex. les listeners sur des éléments graphiques comme les boutons. Ce type de classe permet d'implémenter les méthodes de callback qui sont en réalité des "function wrappers". L’auteur définit une abstraction de cette pratique en offrant une implémentation unique de l’ensemble de ces méthodes de callback largement utilisées dans Java.</p>

<p style="text-align: justify;">La dernière partie de ce chapitre est consacrée à discuter la conception des types en prenant en compte les caractéristiques de la programmation fonctionnelle. Dans un langage fonctionnel pur où toutes les entités sont immutables, les variables ne doivent pas référencer l’objet null. L’auteur prône pour représenter explicitement la possibilité pour un objet d’avoir ou pas une valeur nulle. Ils réifient donc cette possibilité en définissant une classe : Option, puis deux spécialisations de cette classe à savoir : Some et None pour représenter respectivement : une valeur non-nulle et une valeur nulle.</p>

<h1 style="text-align: justify;"><strong>Chapitre 3</strong></h1>

<p style="text-align: justify;">C’est le chapitre le plus important et le plus dense du livre. Il introduit les structures que la programmation fonctionnelle utilise d’une manière efficace pour gérer les données</p>

<h2 style="text-align: justify;"><strong>List (structure différente de java.util.List)</strong></h2>

<p style="text-align: justify;"><strong></strong>La liste est représentée par : head/tête, l’élément le plus à gauche d’une liste, et le reste de la liste qui est appelée tail/file. La file est elle-même une liste qui est représentée par une tête et une file. Nous retrouvons donc une structure récursive pour les listes fonctionnelles. Le chapitre fournit une implémentation en Java de cette structure qui respecte les principes de la programmation fonctionnelle comme grader les valeurs de la liste immutable. L’auteur introduit également une classe : NonEmptyList qui représente les listes vides. La question qui se pose pour cette cette structure de liste fonctionnelle est le cas où on appelle head or tail sur une liste vide. Que sera le comportement attendu ? Lever une exception au risque de ne pas respecter le principe de LSP (Liskov Substituion Principle), ou ne pas lever d'exception et faire à la place un test qui vérifie que la liste n’est pas vide avant d’accéder à l'élément qui représente la tête de la liste ou la file.</p>

<h2 style="text-align: justify;">Opérations de base sur les collections fonctionnelles</h2>

<p style="text-align: justify;">Il existe trois opérations de base que nous appliquons aux collections à savoir : Filter, Map et Fold.</p>

<ol style="text-align: justify;">
    <li><strong>Filter</strong> : crée une nouvelle collection en gardant uniquement les éléments pour lesquelles la méthode pour filtrer renvoie true. La taille de la nouvelle collection sera inférieure ou égale à la taille de la collection d’origine.</li>
    <li><strong>Map</strong> :  crée  une  nouvelle collection où on applique à chaque élément de la collection existante une fonction pour la transformer en valeur.  La taille de la nouvelle collection sera égale à la taille de la collection d’origine .</li>
    <li><strong>Fold</strong> : traverse la collection et utilise chaque élément de la collection pour former une valeur finale en commençant par une valeur de début dite seed. Un exemple d’utilisation de Fold est la somme totale des valeurs entières d’une collection. Pour Fold, on distingue deux fonctions : <strong>FoldLeft</strong> et <strong>FoldRight</strong>, tout dépend du sens choisi pour traverser la collection. Dans des cas, cela n’a aucune importance sur le résultat final comme par exemple l’addition parce que cette opération est associative. Dans d’autres cas, cela amène à avoir des résultats complètement différents suivant le sens par lequel la collection est traversée comme par exemple la soustraction qui n’est pas associative.</li>
</ol>

<blockquote>"foldLeft is the fundamental list iterator and fold Right is the fundamental list recursion operator."</blockquote>

<p style="text-align: justify;">Il y a également la fonction <strong>Reduce</strong> qui est un cas spécialisé du Fold. Elle prend comme valeur seed le premier élément de la collection. Comparé à Fold, l’opération Reduce a l’inconvénient de ne pas s’appliquer à des listes vides parce que ces dernières n’ont pas de premier élément.</p>

<p style="text-align: justify;">Ces trois fonctions peuvent être assemblées /composées ensemble d’une façon à former d’autres fonctions plus complexes.</p>

<h1 style="text-align: justify;"><strong>Chapitre 4, 5 et 6</strong></h1>

<p style="text-align: justify;">Je ne résume pas ces trois chapitres et j'invite le lecteur les lire.</p>

<h1 style="text-align: justify;"><strong>Conclusion </strong></h1>

<p style="text-align: justify;">Je trouve que Dean Wampler nous offre un très bon livre sur la programmation fonctionnelle et comment les concepts de ce paradigme de programmation nous permettent d'améliorer la modularité de nos programmes écrits en objets. Dean Wampler a une maîtrise parfaite des concepts de génie logiciel et est très précis quant à l'utilisation des termes. En cas de différences subtiles, l'auteur prend le soin d'expliquer d'une manière simple les différences et de justifier l'utilisation d'un terme à la place de l'autre. Je cite à titre d'exemple des notions comme type ou classe dont l'auteur prend le soin de donner leurs définitions exactes. L'auteur finit son livre par un glossaire très utile qui résume et rappelle les différentes notions dont le lecteur aura besoin. Ce glossaire a été d'une grande utilité pour moi à la lecture du livre. Enfin, j'ai apprécié l'effort fait pour vulgariser quelques termes, qui sont souvent réservés au monde de la recherche et rarement utilisés dans l'entreprise, en les expliquant d'une manière simple et claire. Je cite par exemple les définitions des entités de première classe, les fonctions d'ordre supérieur, etc.</p>

<h1 style="text-align: justify;">Références</h1>

<ul>
    <li>Functional Programming for Java Developers by Dean Wampler (O'Reilly). Copyright 2011 Dean Wampler, 978-1-449-31103-2.</li>
    <li>[<a title="page web de livre de Dean sur le site d'O'Reilly" href="http://bit.ly/DeanBook">Acheter le livre</a> | <a title="Page web de livre de Dean sur le site d'O'Reilly" href="http://bit.ly/DeanBook">Buy the book</a>]</li>
    <li>[<a title="Site web de Dean Wampler" href="http://www.deanwampler.com/">Plus d'infos sur Dean Wampler</a> | <a title="Site web de Dean Wampler" href="http://www.deanwampler.com/">More about Dean Wampler</a>]</li>
</ul>

<p style="text-align: justify;">Tous les retours et les commentaires sur ce billet sont les bienvenus.</p>
