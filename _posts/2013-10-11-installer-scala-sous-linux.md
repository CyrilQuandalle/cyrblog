---
layout: post
title: Installer Scala sous Linux
date: 2013-10-11 11:45
author: nouhoum traore
comments: true
categories: [développement, Fonctionnel, fonctionnel, Linux, Programmation, scala, ubuntu]
---
<p style="text-align: justify;">Dans ce court post je partage comment j'installe Scala sur mon PC tournant sous Ubuntu. Commencez d'abord par télécharger depuis le site web de Scala le fichier archive (au format .tgz) de la dernière version du langage : <a href="http://www.scala-lang.org/downloads">http://www.scala-lang.org/downloads</a>. La version actuelle de Scala est la <b>2.10</b> et c’est celle que nous allons installer.</p>
<p style="text-align: justify;">Voici comment l'obtenir via la ligne de commande :</p>

<pre>$ wget http://www.scala-lang.org/downloads/distrib/files/scala-2.10.0.tgz</pre>
<p style="text-align: justify;">Ensuite nous extrayons le contenu du fichier archive :</p>

<pre>$ tar xvfz scala-2.10.0.tgz</pre>
<p style="text-align: justify;">Les fichiers sont extraits dans le dossier <b>scala-2.10.0</b>. La sortie suivante de la commande <b>tree</b> montre la structure des fichiers.</p>

<pre>$ cd scala-2.10.0
$ tree -L 1
.
├── bin
├── doc
├── examples
├── lib
├── man
├── misc
└── src</pre>
<p style="text-align: justify;">Les exécutables se trouvent dans le dossier <b>bin</b> et les librairies dans le dossier <b>lib</b>. Sur mon ordinateur les fichiers se trouvent dans <b>~/tools/scala-2.10.0</b> sur lequel j’ai créé le lien symbolique <b>/opt/scala</b> puisque je souhaite pouvoir changer facilement de version de Scala.</p>

<pre>$ sudo ln -s ~/tools/scala/scala-2.10.0 /opt/scala</pre>
<p style="text-align: justify;">Vérifions l’installation en affichant la version de Scala :</p>

<pre>$ /opt/scala/bin/scala -version
Scala code runner version 2.10.0 -- Copyright 2002-2012, LAMP/EPFL</pre>
<p style="text-align: justify;">Ça marche mais ce n’est pas pratique de taper le chemin complet de la commande à chaque lancement de l’interpréteur Scala. Afin de corriger cela j’ajoute le dossier <b>/opt/scala/bin</b> au chemin du système avec la commande suivante :</p>

<pre>$ export PATH=$PATH:/opt/scala/bin</pre>
<p style="text-align: justify;">En mettant la ligne précédente dans le fichier <b>.bashrc</b> les exécutables se trouvant dans <b>/opt/scala/bin</b> seront ajoutés au chemin du système à l’ouverture d’une session.</p>

<pre>$ scala
Welcome to Scala version 2.10.0 (Java HotSpot(TM) 64-Bit Server VM, Java 1.6.0_26).
Type in expressions to have them evaluated.
Type :help for more information.
scala&gt; println ("Now go play with Scala !")
Now go play with Scala !</pre>
