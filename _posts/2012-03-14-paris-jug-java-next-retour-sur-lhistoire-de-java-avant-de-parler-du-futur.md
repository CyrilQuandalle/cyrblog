---
layout: post
title: "Paris JUG Java.next() : retour sur l'histoire de Java (avant de parler du futur)"
date: 2012-03-14 11:41
author: cyrille martraire
comments: true
categories: [Evénements, java, java7, java8, jdk, jigsaw, jvm, lambda, langage, nio, openjdk, parisjug, Programmation, syntaxe, version]
---
<p style="text-align: justify;">Alors que certains grincheux annoncent la fin de vie de Java depuis quelques temps, le Paris JUG propose de faire le point sur Java, le langage, mais aussi la JVM lors d'une soirée dédiée en particulier à Jigsaw et aux lambdas. Je retiens de ces présentations que Java est encore loin d'être distancé, en langage et encore moins comme plateforme, surtout si on s'intéresse aux performances !</p>

<h1 style="text-align: justify;">Java - depuis 1996</h1>

<p style="text-align: justify;">Alexis Moussine-Pouchkine, speaker de la première session a commencé par nous rappeler la déjà longue histoire de Java, depuis ses débuts en 1996 avec un Duke qui saute et qui danse dans les applets : c'était super cool à montrer aux copains qui n'en revenaient pas. C'était vraiment cool et ceux qui ont connu cette période s'en rappellent forcément.</p>

<p style="text-align: justify;">Puis on est passé à une "course à l'armement" avec tout ce qu'il faut pour parler aux bases de données, des "modèles de composants" (EJB anyone?), Swing, et bien sûr la machine Hotspot, avec de la compilation dynamique à partir du profiling d'utilisation.  On eu droit à pleins de bonnes choses, puis à la "période XML qui allait remplacer même Java" selon certains ; on a eu le JCP, un nouveau logo, Java 5, des nouveaux mots-clés et les fameuses annotations, très utilisées "et parfois même abusées".</p>

<p style="text-align: justify;">Le monde Java a connu des améliorations de performance toujours constantes, l'arrivée du monitoring qui fait que la JVM n'est plus une boîte noire, avec <a href="http://java.sun.com/developer/technicalArticles/J2SE/jconsole.html">JConsole</a> et <a href="http://visualvm.java.net/">VisualVM</a>.  Java 6 est arrivé en 2006, puis plus rien. Enfin en 2011, après beaucoup d'incertitudes arrive Java 7, aujourd'hui disponible, et le <a href="http://blog.developpez.com/adiguba/p8012/java/7-dolphin/modif-du-project-coin/">projet Coin</a> (JSR 334), qui propose beaucoup de petites améliorations, notamment :</p>

<ul style="text-align: justify;">
    <li>Switch sur des chaînes de caractères</li>
    <li>La syntaxe en diamant</li>
    <li>Multi catch et try with resource (et l'interface AutoCloseable)</li>
    <li>Simplified Varargs</li>
    <li>Exceptions nettoyées et plus agréables à utiliser</li>
</ul>

<h1 style="text-align: justify;">Java 7 - on attend quoi pour en profiter ?</h1>

<p style="text-align: justify;">Le projet Coin apporte les premiers changements sur le langage depuis 2004, année de Java5.</p>

<p style="text-align: justify;">A la question "Qui utilise Java 7 ?" peu de mains se lèvent, tandis qu'il reste encore des mains qui déclarent utiliser Java 5 ou même Java 1.4. J'ai en effet souvent vu les banques traîner plusieurs versions de retard, par "prudence".</p>

<p style="text-align: justify;">Alexis évoque ensuite le framework ForkJoin, et la classe ParallelArray. On trouve aussi la New "New IO": NIO2. Les NIO2 ont l'avantage de proposer des "higher-level abstractions" par exemple une classe Files avec des méthodes riches, et permet aussi de créer son propre système de fichier, par exemple en mémoire. NIO2 offre aussi l'accès aux méta-data (par exemple les droits à la chmod), et la classe <a href="http://docs.oracle.com/javase/7/docs/api/java/nio/file/Path.html">Path</a>, qui vient remplacer tout ce qui ressemble à un chemin de fichier.</p>

<p style="text-align: justify;">Java 7 apporte aussi le InvokeDynamic coté JVM, dont on reparlera pour les implémentations des lambdas... La JVM supporte de nombreux nouveaux langages, dont les performances ne sont pas toujours au même niveau, notamment à cause de leurs capacités dynamiques : le invokedynamic apporte une solution et aide désormais JRuby par exemple à revendiquer d'être l'implémentation la plus rapide disponible aujourd'hui !</p>

<p style="text-align: justify;">Pour clore ce survol super rapide et simplifié de Java 7, mentionnons que JDBC et RowSet implémentent désormais <a href="http://docs.oracle.com/javase/7/docs/api/java/lang/AutoCloseable.html">AutoCloseable</a>. En vrac on trouve aussi le support de CSS en Javadoc, pour un look plus moderne, et une classe utilitaire <a href="http://docs.oracle.com/javase/7/docs/api/java/util/Objects.html">Objects</a>, par exemple pour la comparaison profonde entre objets.</p>

<p style="text-align: justify;">Java 7 apporte pas mal d'améliorations, "du coup on prend le temps de faire un Java 8 plus ambitieux", avec déjà une preview sur Mac OSX. A noter que si vous ne faites pas de Java 7 aujourd'hui, fin 2012 aura lieu la dernière update publique de Java 6 (version 35 environ); sinon vous pouvez prendre un contrat de support ou passer à Java 7.</p>

<p style="text-align: justify;">A noter que la présentation rapide de Java 7 ne mentionnait pas le <a href="http://blog.thetaphi.de/2011/10/java-7-update-1-released-does-it-fix.html">fameux bug relevé par Uwe</a>. Cela dit Java 7 est désormais "safe" si on prend soin de ne pas activer l'optimisation "aggressive"...</p>

<h1 style="text-align: justify;">2013 sera l'année de Java 8</h1>

<p style="text-align: justify;">Avec au menu :</p>

<ul style="text-align: justify;">
    <li>Jigzaw, un vrai système de modules</li>
    <li>Les lambdas</li>
    <li>JavaFX 3.0 : mise en open source entre temps</li>
    <li>Complete Oracle VM convergence : y compris Mission Control et Flight Recorder, pour un monitoring sur période glissante par exemple</li>
    <li>Javascript on VM</li>
    <li>Aller vers le plus petit et être capable de tourner sur des périphériques avec mémoire et processeur réduits, du multicouche au travers de JavaFX</li>
    <li>Supprimer la "permanent generation"</li>
    <li>Smaller VM (moins de 3 Mo)</li>
    <li>Et l’intégration de Joda time, enfin :)</li>
</ul>

<p style="text-align: justify;">En conclusion, allez voir Open JDK pour une préview et juger par vous-même, avec tous ses JEPs (Enhancement Proposals) "Seul le code fait foi !"</p>

<p style="text-align: justify;"></p>
