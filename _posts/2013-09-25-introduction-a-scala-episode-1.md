---
layout: post
title: Introduction à Scala - épisode 1
date: 2013-09-25 16:29
author: nouhoum traore
comments: true
categories: [développement, Fonctionnel, fonctionnel, Programmation, scala]
---
<p style="text-align: justify;">Scala est un langage qui a du caractère. Il fait réagir du monde; ceux qui l’ont essayé et l’adorent, ceux qui ne l’ont jamais essayé mais le détestent et les autres. Parmi ce beau monde les plus étonnants sont ceux qui veulent accuser Scala des travers des langages (comme le C++) qu’ils ont aimé détester. Au lieu de vous laisser influencer par les autres essayez par vous-même ce langage qui a fait le pari audacieux de marier les paradigmes fonctionnel et orienté objet.</p>

<h3 style="text-align: justify;">A propos de cette série d’articles</h3>
<p style="text-align: justify;">Je souhaite à travers une série d’articles introduire le langage de programmation Scala. Cela a un double intérêt pour moi. D’une part ce sera l’occasion pour moi d’approfondir ma connaissance du langage et d’autre part j'accompagne les premiers pas de ceux qui souhaitent l’apprendre.</p>

<h3 style="text-align: justify;">D’où vient Scala et que propose-t-il ?</h3>
<p style="text-align: justify;">Scala est un langage de programmation créé par Martin Ordersky professeur à l’École Polytechnique Fédérale de Lausane EPFL en Suisse. Sa première version est rendue publique en 2003. Son nom vient de l’abréviation de “scalable language” signifiant qu’il est capable de s’adapter aux contraintes de passage à l’échelle (scalabilité) du développeur en lui fournissant des “abstractions” et des constructions à cet effet. La particularité de Scala est qu’il intègre à la fois les paradigmes de programmation fonctionnelle et orientée objet. Il est statiquement typé et s’exécute sur la machine virtuelle Java (JVM).</p>
<p style="text-align: justify;">Dans la suite de la série je reviendrai plus en détail sur ces deux paradigmes. Le point clé de ce mariage entre les paradigmes orienté objet et fonctionnel est qu’il vous offre plus d’options pour résoudre vos problèmes. Les différentes possibilités d’extension du langage facilitent l’expression de logiques métier complexes et de produire du code qui s’intègrent d’une manière élégante au reste du langage. La bibliothèque standard de Scala est un bon exemple de cette puissance expressive.</p>
<p style="text-align: justify;">Un autre aspect de Scala est qu’il dispose d’une inférence de type qui allège le code et en réduit fortement la verbosité. C’est agréable et on a parfois l’impression de travailler avec un langage dynamique.</p>
<p style="text-align: justify;">Scala vient avec un interpréteur qui porte le doux nom de REPL pour Read-Eval-Print-Loop. Comme vous l’avez peut-être deviné le REPL vous laisse taper du code dans une console, l’évalue, affiche le résultat et attend de nouveau que vous tapiez quelque chose. Vous pouvez vous servir du REPL pour tester rapidement vos idées et faire du prototypage. Il est idéal pour apprendre le langage et nous ne manquerons pas de nous en servir pour cela.</p>

<h3 style="text-align: justify;">Quel écosystème pour Scala ?</h3>
<p style="text-align: justify;">La plate-forme Java est aujourd’hui très mature et bien introduite en entreprise. Les outils et les librairies sont nombreuses et matures. En outre les développeurs Java se comptent en milliers. Scala tire pleinement partie de cette maturité et de la puissance qu’elle confère à Java en s’intégrant facilement avec le langage Java. Un programme écrit en Scala peut accéder à n’importe quel code écrit en Java et tourne, après compilation, sur la machine virtuelle Java.</p>
<p style="text-align: justify;">Au-delà de la force que lui confère Java, l’écosystème de Scala compte de nombreux projets et outils emblématiques dont voici quelques uns :</p>

<ul style="text-align: justify;">
	<li><b>Akka</b> : Akka propose un ensemble d’outils pour créer des applications concurrentes et distribuées. Il utilise les <a href="http://fr.wikipedia.org/wiki/Mod%C3%A8le_d%27acteur">acteurs</a> comme modèle de programmation.</li>
	<li><b>Play</b> : Un framework pour la création d’applications web</li>
	<li><b>Scalding</b> : Une librairie Scala qui rend facile la spécification des tâches Hadoop.</li>
	<li><b>Specs2 et ScalaTest</b> : Des librairies de test populaires écrits en Scala.</li>
	<li><b>Spray</b> : Basé sur Akka, Spray propose des interfaces de programmation (API) pour écrire des applications HTTP performantes</li>
</ul>
<h3 style="text-align: justify;">Les outils, les outils !</h3>
<p style="text-align: justify;">Commençons par les outils de build. Un projet Scala peut être buildé par les outils de build majeurs en Java : Maven, Gradle et Apache buildr. En outre il existe un autre outil, SBT (Simple Build Tool), écrit en Scala pour builder des projets Scala et Java. SBT est utilisé par des projets comme Akka, Play, Spray etc.</p>
<p style="text-align: justify;">Eclipse, Netbeans et Intellij ont des plugins pour l’intégration Scala. Il est aussi possible d’utiliser d’autres environnement comme Sublime Text, emacs, vim etc.</p>

<h3 style="text-align: justify;">Autres ressources pour apprendre à programmer en Scala</h3>
<p style="text-align: justify;">Il existe d’autres ressources disponibles gratuitement sur Internet pour apprendre à programmer en Scala. Voici une liste non exhaustive de certaines de ces ressources :</p>

<ul style="text-align: justify;">
	<li><b><a href="http://docs.scala-lang.org">http://docs.scala-lang.org</a></b> : Un site plutôt bien fait et assez complet pour apprendre le langage. Vous y trouvez une quantité incroyable d’information.</li>
	<li>La première édition du <b>livre écrit par Odersky</b>, créateur de Scala, est en accès libre en ligne à cette adresse : <a href="http://www.artima.com/pins1ed/">http://www.artima.com/pins1ed/</a>.</li>
	<li>Scala School par Twitter : Twitter a publié une introduction rapide à Scala. Vous pouvez y jeter un coup d’œil en allant à l’adresse http://twitter.github.io/scala_school/.</li>
	<li>Martin Odersky donne un cours d’introduction à la programmation fonctionnelle à travers Scala sur <a href="https://www.coursera.org">https://www.coursera.org</a>. Environ 50 000 personnes se sont inscrites à la première édition du cours. Faites sur un tour sur coursera afin de vous renseigner sur les éditions à venir.</li>
</ul>
<h3 style="text-align: justify;">J’ai besoin de vous !</h3>
<p style="text-align: justify;">N’hésitez pas à réagir à ce que j’écris ici ou à me corriger à travers les commentaires et à me faire des suggestions sur le contenu et la forme des articles.</p>

<h3 style="text-align: justify;">C’est quoi la suite ?</h3>
<p style="text-align: justify;"><a title="Introduction à Scala, épisode 2 – premiers pas avec Scala" href="http://www.arolla.fr/blog/2013/10/introduction-a-scala-episode-2-premiers-pas-avec-scala/">Dans le prochain nous allons installer Scala et faire nos premiers avec le REPL. Ce sera pour vous l’occasion d’écrire du code Scala.</a></p>
