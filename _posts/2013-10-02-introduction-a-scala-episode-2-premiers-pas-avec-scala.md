---
layout: post
title: Introduction à Scala, épisode 2 - premiers pas avec Scala
date: 2013-10-02 11:00
author: nouhoum traore
comments: true
categories: [développement, Fonctionnel, fonctionnel, Programmation, scala]
---
<p style="text-align: justify;">Après une introduction générale à Scala et à son écosystème dans le <a title="Introduction à Scala – épisode 1" href="http://www.arolla.fr/blog/2013/09/introduction-a-scala-episode-1/">premier article</a>, nous allons installer Scala puis écrire nos premières lignes de code Scala afin de nous familiariser avec le REPL.</p>

<h3 style="text-align: justify;">Télécharger Scala</h3>
<p style="text-align: justify;">Pour fonctionner Scala a besoin de Java. Vous devez donc vous assurer que Java est installé sur votre machine avant de procéder à l’installation de Scala. Dans cette série d’articles nous utiliserons la version 2.10.2 de Scala et cela requiert au moins la version 1.6 de Java. Commencez d’abord par vérifier la disponible de Java sur votre système avec la commande suivante :</p>

<pre>$ java -version</pre>
<p style="text-align: justify;">En exécutant cette commande sur ma machine j’obtiens la sortie suivante :</p>

<pre>$ java -version
java version "1.6.0_51"
Java(TM) SE Runtime Environment (build 1.6.0_51-b11-457-11M4509)
Java HotSpot(TM) 64-Bit Server VM (build 20.51-b01-457, mixed mode)</pre>
<p style="text-align: justify;">Vous devez avoir une sortie similaire si Java est installé sur votre machine sinon rendez-vous à l’adresse http://www.java.com pour le télécharger.</p>
<p style="text-align: justify;">Maintenant que vous avez installé Java procédons à l’installation de Scala. En fonction de votre plate-forme elle ne se fera pas de la même manière.</p>

<h4 style="text-align: justify;">Windows</h4>
<p style="text-align: justify;">Allez à l’adresse <a href="http://www.scala-lang.org/download/2.10.2.html">http://www.scala-lang.org/download/2.10.2.html</a> et cliquez sur le lien nommé <b>scala-2.10.2.msi</b>. Cela va télécharger un fichier qui permet d’installer Scala sur votre machine Windows. Une fois le téléchargement terminé il suffit de cliquer dessus et de suivre les différentes étapes de l’installation. Une fois l’installation terminée vous pouvez vérifier si cela a marché avec la commande :</p>

<pre>$ scala -version</pre>
<p style="text-align: justify;">Vous obtiendrez une sortie similaire à :</p>

<pre>$ scala -version
Scala code runner version 2.10.2 -- Copyright 2002-2013, LAMP/EPFL</pre>
<h4 style="text-align: justify;">Unix/Linux, Mac OS X et Cygwin</h4>
<p style="text-align: justify;">Téléchargez le fichier archive disponible à l’adresse <a href="http://www.scala-lang.org/files/archive/scala-2.10.2.tgz">http://www.scala-lang.org/files/archive/scala-2.10.2.tgz</a> puis extrayez-le à l’endroit de votre choix. Sur ma machine je vais l’installer dans /usr/local/share.</p>

<pre>$ cd /usr/local/share/
$ sudo wget http://www.scala-lang.org/files/archive/scala-2.10.2.tgz</pre>
<p style="text-align: justify;">Une fois le téléchargement terminé j’extrais les fichiers :</p>

<pre>$ tar xvfz scala-2.10.2.tgz</pre>
<p style="text-align: justify;">Histoire de ne pas lier mon installation à une version particulière je crée un lien symbolique vers le dossier scala-2.10.2.</p>

<pre>$ sudo ln -s /usr/local/share/scala-2.10.2 /usr/local/share/scala</pre>
<p style="text-align: justify;">Ensuite éditez par exemple votre fichier .bashrc pour lui ajouter les lignes suivantes :</p>

<pre>SCALA_HOME=/usr/local/share/scala
export PATH=$PATH:$SCALA_HOME/bin</pre>
<p style="text-align: justify;">Enfin vous pouvez vérifier l’installation comme suit :</p>

<pre>$ source ~/.bashrc
$ scala -version
Scala code runner version 2.10.2 -- Copyright 2002-2013, LAMP/EPFL</pre>
<h3 style="text-align: justify;">Que contient l’installation de Scala ?</h3>
<p style="text-align: justify;">Faisons le tour du propriétaire pour comprendre ce que contient notre installation de Scala. Pour cela je me place dans le dossier où est installé Scala puis je lance la commande tree :</p>

<pre>$ cd /usr/local/share/scala
$ tree -d -L 2</pre>
<p style="text-align: justify;">Cela me donne la structure suivante :</p>

<pre>$ tree -d -L 2
.
├── bin
├── doc
│   └── tools
├── examples
│   ├── actors
│   ├── monads
│   ├── parsing
│   ├── tcpoly
│   └── xml
├── lib
├── man
│   └── man1
├── misc
│   └── scala-devel
└── src

15 directories</pre>
<p style="text-align: justify;">Voici une brève description des dossiers ci-dessus :</p>

<ul style="text-align: justify;">
	<li><b>bin</b> : Ce dossier contient les programmes exécutables comme le compilateur de Scala scalac, le dé-compilateur scalap ou l'interpréteur scala. Lors de l’installation ce dossier a été ajouté au chemin où le système d’exploitation cherche les commandes que nous tapons dans le terminal. Ainsi nous n’avons pas besoin de taper le chemin complet des exécutables.</li>
	<li><b>doc</b> : Ce dossier contient de la documentation sur les commandes localisées dans le repertoire bin.</li>
	<li><b>examples</b> : Ici vous trouverez des exemples de programmes écrits en Scala. Faites-y un tour afin de voir à quoi ressemble la syntaxe de Scala ou pour trouver de l’inspiration.</li>
	<li><b>lib</b> : C’est là que résident les librairies c’est-à-dire un ensemble de fonctions et de classes prêtes à l’emploi que vous pouvez utiliser pour écrire vos propres programmes.</li>
	<li><b>man</b> : Le contenu de ce dossier est utilisé par la commande man qui affiche l’aide sur des commandes comme scala ou scalac. Essayez ça :
<pre>$ man scala</pre>
</li>
	<li><b>src</b> : Le code source de Scala et des librairies se trouvent ici.</li>
	<li><b>misc</b> : Ce dossier est, j’ai envie de dire, sans grand intérêt pour nous. A ce jour il contient le plugin pour la <a href="http://fr.wikipedia.org/wiki/Continuation">contituation</a>.</li>
</ul>
<h3 style="text-align: justify;">Premiers pas avec l’interpréteur de Scala ou le REPL</h3>
<p style="text-align: justify;">Comme vous allez vous en rendre compte, l’interpréteur de Scala est un outil puissant pour expérimenter des choses et accélérer son apprentissage du langage. Vous avez un doute et hop lancez l’interpréteur pour le lever ! Vous avez une idée et hop l’interpréteur est là pour la tester.</p>
<p style="text-align: justify;">Et pour lancer l’interpréteur rien de plus simple que de taper la commande <i>scala</i> dans une console :</p>

<pre>$ scala
Welcome to Scala version 2.10.2 (Java HotSpot(TM) 64-Bit Server VM, Java 1.6.0_51).
Type in expressions to have them evaluated.
Type :help for more information.
scala&gt;</pre>
<p style="text-align: justify;">Vous voilà dans l’interpréteur prêt à taper des commandes. Tapez un nombre et appuyez sur la touche Enter du clavier pour voir.</p>

<pre>scala&gt; 20
res0: Int = 20</pre>
<p style="text-align: justify;">Le nombre <b>20</b> sur la première ligne correspond à ce que j’ai tapé et la deuxième ligne à ce que me répond l’interpréteur. Que s’est-il passé ? L’interpréteur a récupéré ce que j’ai tapé (ici 20), l’a évalué puis a affiché le résultat en lui donnant le nom <b>res0</b> (pour résultat numéro 0) et la valeur 20. Désormais <i>res0</i> contient le nombre 20. Pour vous en convaincre tapez <i>res0</i> dans l’interpréteur :</p>

<pre>scala&gt; res0
res1: Int = 20</pre>
<p style="text-align: justify;">Note : Notez que le nom res0 est suivi de <b>“: Int”</b>. Cette annotation nous précise que le nom <i>res0</i> contient un entier. Nous reviendrons sur ce point dans la suite.</p>
<p style="text-align: justify;">Recommençons avec une opération un peu plus compliquée : l’addition de deux nombres, par exemple <b>2 + 7</b>.</p>

<pre>scala&gt; 2 + 7
res2: Int = 9</pre>
<p style="text-align: justify;">Là encore le REPL a évalué ce que nous avons tapé puis a affiché le résultat.</p>
<p style="text-align: justify;">Il est aussi possible de réutiliser le résultat d’une opération précédente :</p>

<pre>scala&gt; res2 + 2
res3: Int = 11
scala&gt; res3 - 8
res4: Int = 3</pre>
<p style="text-align: justify;">En dehors du code Scala l’interpréteur de Scala accepte aussi d’autres commandes dont la plus importante est <b>:help</b>. Elle vous listera la liste des commandes disponibles. Pour l’instant je mets l’accent sur trois commandes seulement dont voici la description :</p>

<ul style="text-align: justify;">
	<li><b>:cp</b> : Cette commande vous permet d’utiliser depuis le REPL du code situé dans un fichier archive Java (avec l’extension .jar) ou dans un répertoire.</li>
	<li><b>:quit</b> : Celle-là est utilisée pour quitter le REPL. Eh oui il faut pouvoir en sortir !</li>
	<li><b>:paste</b> : Cette commande s’avère assez pratique quand on écrit du code sur plusieurs lignes dans le REPL.</li>
</ul>
<p style="text-align: justify;">Même si le REPL est sympathique comme calculatrice elle peut faire bien plus comme afficher un message sur l’écran par exemple.</p>

<pre>scala&gt; println("Bonjour ! Ça se passe bien jusque là ?")
Bonjour ! Ça se passe bien jusque là ?</pre>
<p style="text-align: justify;">Essayez la même chose chez vous, n’hésitez pas à changer le message. Ici nous avons utilisé la fonction <b>println</b> pour afficher notre message à l’écran. Nous verrons dans la suite ce qu’est une fonction mais si vous êtes pressé de le savoir lisez <a href="http://www.arolla.fr/blog/2013/01/les-fonctions-et-les-fonctions-d%E2%80%99ordre-superieur/">cet article</a>.</p>

<h3 style="text-align: justify;">Résumé et programme pour la suite</h3>
<p style="text-align: justify;">Mine de rien nous avons déjà fait beaucoup de choses. Nous avons installé Scala, appris à lancer le REPL et à le faire exécuter du code Scala pour nous. Dans le prochain article nous nous baserons sur ces connaissances pour apprendre de nouveaux concepts et nous initier à la syntaxe de Scala.</p>
