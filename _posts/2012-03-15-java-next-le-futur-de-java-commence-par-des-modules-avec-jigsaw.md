---
layout: post
title: "Java.next() : le futur de Java commence par des modules avec Jigsaw"
date: 2012-03-15 10:24
author: cyrille martraire
comments: true
categories: [dependance, Evénements, java, java8, jigsaw, maven, module, package, parisjug, Programmation]
---
<p style="text-align: justify;">Au menu du prochain Java 8, prévu pour 2013, la modularité dans Java grâce au projet Jigsaw. Alexis Moussine-Pouchkine, speaker au Paris JUG pour cette soirée Java.next() nous présente cette <strong>grosse </strong>innovation. Des vrais modules, c'est pas rien !</p>

<h1 style="text-align: justify;">Modules dans Java, c'était pas gagné...</h1>
<p style="text-align: justify;">Pour rappel, il a eu plusieurs tentatives depuis des années :</p>

<ul style="text-align: justify;">
	<li>JSR 277 : Ça l'a pas fait</li>
	<li>OSGI : Ça l'a pas fait non plus</li>
	<li>Superpackages JSR 294 : Encore raté</li>
	<li>Jigsaw : Cette fois c'est la bonne, avec en fond d'écran un joli puzzle</li>
</ul>
<p style="text-align: justify;">Aujourd'hui en Java on connaît encore pas mal de douleurs (pain points), notamment sur la construction et le packaging de son application. "On va faire de l’intégration continue, ça aide, puis on finit quand même par se dire qu'il faut des bons outils". On rencontre encore trop facilement la situation de "Jar Hell" où l'ordre du classpath compte ! Des commentaires dans la salle confirment à voix basse que ça arrive.</p>
<p style="text-align: justify;">Autre constat, la dépendance au JRE ne vaut pas la peine si c'est juste pour refaire une petite application comme <em><a href="http://curl.haxx.se/">curl</a></em>. On voudrait pouvoir exprimer une dépendance sur une portion réduite de l'API Java, contrairement au rt.jar d'aujourd'hui qui fait pas loin de 15 Mo en un seul tenant. Enfin on veut aussi gagner en terme de temps de démarrage.</p>
<p style="text-align: justify;">Jigsaw nous propose alors un système de module simple, statique, puissant, qui peut s'appliquer au JDK lui-même et peut améliorer les performances. Première conséquence : plus de classpath ! C'est tout simplement énorme ! J'ai envie d’applaudir.</p>
<p style="text-align: justify;">C'est aussi la fin du rt.jar. Par contre tout ça est encore seulement en prototype.</p>

<h1 style="text-align: justify;">Modularité</h1>
<p style="text-align: justify;">Jigsaw propose d'introduire les concepts de modularité directement dans le langage, grâce à des fichiers réservés nommés "module-info.java" (c'est la même astuce que pour les package-info.java utilisés pour Javadoc, le tiret est normalement illégal comme nom de fichier). Ce fichier module-info.java fait partie du source Java, dans lequel on déclare un module avec une syntaxe inspirée d'une classe :</p>
<p style="text-align: justify;"><code>module foo { }</code></p>
<p style="text-align: justify;">On exprime les versions avec le signe @, par exemple :</p>

<pre><code>module foo @ 1.0 { }</code></pre>
<p style="text-align: justify;">Un module sert avant tout à grouper des classes d'un ou plusieurs packages pour les exporter :</p>

<pre>module foo {
	exports foo; // Export all public types in the foo package
}</pre>
<p style="text-align: justify;">Un module peut dépendre d'un autre via le mot-clé "<em>requires</em>", ce qui permet les dépendances transitives :</p>

<pre>module bar {
    requires org.bar.lib @ 2.1-alpha; // Depends upon the module org.bar.lib version 2.1-alpha
}</pre>
<p style="text-align: justify;">On utilisera le mot-clé "<em>permits</em>" pour ne permettre d'utiliser une classe que par soi-même (encapsulation), et le mot-clé "<em>provides</em>" pour faire des modules aliases au-dessus de plusieurs dépendances, ou pour faire du splitting : découper une application en plusieurs parties, en utilisant un module vide qui dépend des parties découpées, dont on dépendra ensuite. Tout ça est inspiré des packages Debian. On peut aussi déclarer le point d'entrée "main" avec le mot-clé "class" :</p>

<pre>module com.greetings @ 0.1 {
    requires ...
    class com.greetings.Hello; // the main entry point
}</pre>
<p style="text-align: justify;">Alexis nous parle ensuite de <strong>packaging = module files + libraries + repository + native library</strong></p>
<p style="text-align: justify;">Contrairement à beaucoup je ne suis pas un fan de la ligne de commande, mais dans notre cas c'est assez pédagogique :</p>
<p style="text-align: justify;">On <strong>compile</strong> un module avec javac en passant le modulepath :</p>

<pre>javac direct -modulepath modules -sourcepath src ...</pre>
<p style="text-align: justify;">On peut <strong>créer une library de modules</strong> avec <em>jmod</em>, un outil pour manipuler les librairies et sa commande <em>create</em> :</p>

<pre>jmod -L mylib create</pre>
<p style="text-align: justify;">Chaque library a un parent, explicite ou implicite. On <strong>installe les bibliothèques</strong> avec la commande <em>install</em> de <em>jmod</em> :</p>

<pre>jmod -L mylib install modules org.astro org.bar.lib</pre>
<p style="text-align: justify;">Cerise sur le gâteau, si on peut dire car le but est bien d'exécuter le module, on <strong>lance le module</strong> avec <em>java</em> :</p>

<pre>java -L mylib -m org.astro org.bar.lib</pre>
<p style="text-align: justify;">Un <strong>repository est une library qui a été mise en ligne</strong> sur une url, et qui expose un nombre de modules *.jmod.</p>
<p style="text-align: justify;">Enfin on <strong>package</strong> le module comme un jmod file avec la commande <em>jpkg</em>:</p>

<pre>$ jpkg -m modules/com.greetings jmod com.greetings</pre>
<p style="text-align: justify;">On peut aussi faire des packages natifs, par exemple au format debian .deb avec <em>jpkg</em> et l'option <em>deb</em>.</p>
<p style="text-align: justify;">A noter qu'à écouter les exemples donnés par Alexis on remarque qu'en 2012 on se préoccupe de la taille des dépendances ! On peut ainsi vérifier la taille à télécharger puis telle qu'elle sera sur disque d'un module avant de décider de le télécharger.</p>

<h1 style="text-align: justify;">Slicing the JDK</h1>
<blockquote>On devrait pas avoir à tirer le package Corba pour faire une application Hello World !</blockquote>
<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/03/photo.jpg"><img class="alignnone size-medium wp-image-341" title="photo" src="http://www.arolla.fr/blog/wp-content/uploads/2012/03/photo-300x225.jpg" alt="" width="300" height="225" /></a></p>
<p style="text-align: justify;">C'est un vrai exemple qui fait sourire. On le sait peu, il a déjà eu un effort pour simplifier les dépendances, sans rien casser, dans le JDK 7, avec des modules identifiés et des noms qui ont du sens, avec par exemple un sous-ensemble "headless" qui ne dépend pas de tout ce qui est graphique. Pour Java 8 cette démarche sera beaucoup plus aboutie.</p>
<p style="text-align: justify;">On évoquait OSGI, et le projet Penrose proposera une certaine forme d'interopérabilité avec OSGI. Selon Alexis : "On ne peut pas utiliser OSGI, ca ne fonctionne qu'après avoir booté la JVM". Des décisions ont donc été prises vers ce projet Penrose pour faire en sorte que les applications cohabitent. Parmi les 4 niveaux d'interopérabilité, on est actuellement au niveau 0. On peut aller plus loin pour permettre à OSGI d'exploiter les métadata de Jigsaw, et c'est un travail en cours.</p>
<p style="text-align: justify;">Les applications existantes fonctionneront toujours sans changement. On pourra encore utiliser le classpath, mais il ne faudra pas dépendre directement sur rt.jar dans la structure interne du JDK. Les classes non standard, par exemple en sun.* ne seront peut-être plus accessibles.</p>
<p style="text-align: justify;">Les jar ne vont disparaitre en un jour. On parle même d'un mode de compatibilité, et on pourra "Jigsaw-iser" ses jars avec jmod.</p>

<h1 style="text-align: justify;">La fin de Maven ?</h1>
<p style="text-align: justify;">Les commentaires à la pause autour du buffet évoquaient fréquemment la fin de Maven. C'est vrai qu'on ne peut pas ne pas y penser !</p>
<p style="text-align: justify;">Cela dit on ne va pas remplacer tous les projets existants juste pour le plaisir, sauf si c'est vraiment facile et automatisé. On exprime déjà nos dépendances avec Maven ; pourquoi ne pas reprendre cette information ? D'un POM vers une librarie Jigsaw, un prototype s'attelle à permettre cette migration mais "c'est pas gagné" selon la qualité de votre POM. Cette migration est déjà implémentée dans le prototype mais s'avère très difficile, avec des soucis par exemple avec les dépendances dupliquées. On verra si ça survivra au JDK 8… sinon ça fera le bonheur des vendeurs d'outils annexes.</p>
<p style="text-align: justify;">En tout cas cette présentation sur Jigsaw m'a enthousiasmé car un concept de module simple et rigoureux et vraiment standard manquait vraiment à la plateforme. Jigsaw est clairement une bonne nouvelle !</p>
