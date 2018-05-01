---
layout: post
title: Dompter VIM en trois temps 0/3
date: 2015-01-30 08:42
author: yakhya dabo
comments: true
categories: [Bonnes pratiques de dév, editeur, Linux, outils, Outils, Programmation, vi, vim]
---
<p align="center"><span style="font-size: 32px; line-height: 44.7999992370606px;"><b>Découverte de VIM</b></span></p>

<p align="justify">Dans le livre <strong><em>The Pragmatic Programmer</em></strong>, l'un des must read du craftsman, les auteurs (<b>Andy Hunt</b> et <b>Daves Thomas</b>) insistent sans cesse sur le principe D.R.Y (Don't Repeat Yourself). Il revient sous forme de leitmotiv dans tous les chapitres. Le bon développeur, en vrai artiste, ne se répète pas. En plus de son langage de programmation fétiche (Java, Scala, Ruby, ...) il doit disposer d'un IDE configurable dans sa boîte à outils, être à l'aise avec la programmation shell pour automatiser les tâches courantes, et maîtriser du bout des doigts un éditeur de texte, “<em>I</em><i>t is better to know one editor very well, and use it for all editing tasks</i>.” Puis, dans un style messianique, ils décrivent l'éditeur du développeur : “<i>The editor will be an extension of your hand; ... </i><i>Make sure that the editor you choose is available on all platforms you use.”</i> L'éditeur doit être <b>configurable</b>, <b>extensible</b> et <b>programmable</b>. Et voilà pourquoi je veux vous présenter VIM comme étant l'éditeur du Développeur.</p>

<p style="text-align: center;" align="justify"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/vim_developer.png"><img class="aligncenter size-full wp-image-2957" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/vim_developer.png" alt="vim_developer" width="588" height="225" /></a></p>

<p align="justify">VIM (Vi IMproved) est un éditeur de texte libre, un parmi la vingtaine de clones de Vi. Pour rappel Vi existe depuis 1976; une antiquité dans le temps informatique. Il a survécu à toutes les tempêtes qui ont secoué le monde informatique. A l'origine Vi a été créé pour le système Unix dont il deviendra plus tard l'éditeur standard. C'est aussi un éditeur modal; les touches du clavier sont interprétées différemment d'un mode à un autre. En mode <i><b>Insert</b></i> pour taper du texte ou en mode <i><b>N</b></i><i><b>ormal</b></i> pour exécuter des commandes.</p>

<p align="justify">La première release de Vim voit le jour le 2 Novembre 1991. Il sera publié sous licence GNU et disponible sur plusieurs plateformes, Linux, Windows et Mac étant les plus populaires. Le projet a été initié par <b>Bram Moolenaar </b>pour pallier les imitations de Vi en ajoutant de nouvelles fonctionnalités :</p>

<ul>
    <li>Le support de plugins</li>
    <li>L'édition de fichier, via le réseau, avec les protocoles SSH et Http</li>
    <li>L’interaction avec la souris</li>
    <li>La coloration syntaxique, notamment pour les langages de programmation</li>
    <li>L'intégration d'un langage de script</li>
</ul>

<p align="justify">L'esprit de Vim est de réaliser toutes les actions possibles sans avoir besoin de lever les mains du clavier, en se passant de la souris et des touches éloignées. Sa courbe d'apprentissage en forme de mur (<i>Learning wall</i>) peut être interprétée de la manière suivante : <i>On ne s'investit qu'une seule fois,</i> <em>pour toujours</em>.</p>

<p style="text-align: center;" align="justify"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/learning_curve1.jpg"><img class="aligncenter size-full wp-image-2959" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/learning_curve1.jpg" alt="learning_curve" width="500" height="333" /></a></p>

<p align="justify">Je vous propose dans cette série de trois articles trois étapes pour dompter VIM. <i><b>L'objectif est de vous convaincre qu'au delà de manipuler des fichiers de logs sur des serveurs distants, Vim peut être notre éditeur de texte au quotidien</b></i>. La première partie présentera les commandes de base de Vim à travers ses principaux modes. La deuxième partie portera sur la configuration, comment adapter l'outil à nos habitudes et nos besoins spécifiques sans violer le principe DRY. Et dans la dernière partie on présentera quelques plugins pour l'extension de Vim. Il est déconseillé de vouloir commencer par connaître toutes les commandes de VIM. C'est pour ça que vous ne les verrez pas toutes. 10% d'entre elles pourront couvrir vos besoins les plus courants.</p>

<p align="left"><span style="font-size: large;"><b>Les </b></span><span style="font-size: large;"><b>Ressources</b></span></p>

<p align="left">Parmi les nombreuses ressources disponibles dans les librairies et sur internet quelques auteurs se distinguent par la qualité très solide de leurs références :</p>

<p align="left"><b>Blogs</b></p>

<ul>
    <li>Steve Losh : <a href="http://stevelosh.com/blog/" target="_blank">http://stevelosh.com/blog/</a></li>
    <li>Tim Pope : <a href="https://github.com/tpope" target="_blank">https://github.com/tpope</a></li>
</ul>

<p align="left"><b>Livres</b></p>

<ul>
    <li>Pragmatical Vim (Drew Neil)</li>
    <li>Learning the vi and Vim Editors (Arnold Robbins, Linda Lamb)</li>
</ul>

<p align="left"><b>Vidéo</b></p>

<ul>
    <li>Bill Odon sur <a href="http://www.infoq.com/presentations/Vim-From-Essentials-to-Mastery" target="_blank">infoQ</a></li>
    <li>Drew Neil : <a href="http://vimcasts.org" target="_blank">http://vimcasts.org</a></li>
</ul>

<p align="left"><span style="font-size: large;"><b>Pour Commencer</b></span></p>

<ul>
    <li>Installer vim (apt-get install vim sur Ubuntu)</li>
    <li>Pour lancer vim depuis le terminal</li>
</ul>

<p align="left"><i><b>vim</b></i> ouvre un éditeur vierge</p>

<p align="left"><i><b>vim fileName</b></i> ouvre ou crée un fichier</p>

<p align="left"><i><b>vim fileName+lineNumber</b></i> ouvre un fichier et met le curseur à la position <i>lineNumber</i></p>

<p align="left">Une fois l'éditeur lancé on peut effectuer les actions suivantes :</p>

<ul>
    <li>Enregistrer : <i><b>:w</b></i><i> </i></li>
    <li>Enregistrer sous <i>fileName</i>: <i><b>:w/fileName </b></i></li>
    <li>Quitter définitivement : <i><b>:q </b></i></li>
    <li>Quitter Vim provisoirement : <b>&lt;Ctrl-</b><b>Z</b><b>&gt; </b>(et <i><b>fg</b></i> pour revenir sur vim)</li>
    <li>Afficher les numéros absolus : <b>:set nu</b></li>
    <li>Afficher les numéros relatifs :<b> :set </b><b>relative</b><b>nu</b><b>mber</b></li>
</ul>

<p align="left"><b>L'aide :</b></p>

<p align="left">Le dernier détail important, qui reste incontournable pour démarrer, c'est l'utilisation de l'Aide. Pour y accéder il suffit de taper <b>:help </b>(ou <b>:h</b>)avec le mot clé en paramètre :</p>

<p align="left"><b>:help </b><b>pattern</b><b> </b>ou<b> :h </b><b>pattern</b></p>

<p align="left">Si vous vous sentez prêts la prochaine étape c'est par là, <a href="http://www.arolla.fr/blog/?p=2967" target="_blank"><i><b>les</b></i><i><b> </b></i><span style="font-size: medium;"><i><b>principaux modes</b></i></span><i><b> de VIM</b></i>.</a></p>
