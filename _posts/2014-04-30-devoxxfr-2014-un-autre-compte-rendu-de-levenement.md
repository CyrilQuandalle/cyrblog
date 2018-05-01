---
layout: post
title: "[DevoxxFr 2014] : Un autre compte rendu de l'événement 1/2"
date: 2014-04-30 11:13
author: yakhya dabo
comments: true
categories: [Actu, devoxxFR, Evénements, GIT, java, java8, Javaee7]
---
<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/05/devoxx_fr_2014_arolla.jpg"><img class="alignnone size-medium wp-image-2449" alt="devoxx_fr_2014_arolla" src="http://www.arolla.fr/blog/wp-content/uploads/2014/05/devoxx_fr_2014_arolla-300x225.jpg" width="300" height="225" /></a></p>

<p style="text-align: justify;">La troisième édition de Devoxx s'est déroulée dans le même lieu que les précédentes, du 16 au 18 Avril 2014, et ce sera probablement la dernière fois, faute de place. L'année prochaine elle se tiendra au Palais des Congrès à la même période. Devoxx est devenu un évènement incontournable pour la communauté Java.</p>

<p style="text-align: justify;">Les sponsors traditionnels étaient présents, mais avec moins d'ambiance dans les stands que lors des deux premières éditions (No baby foot :-( !). Xebia, Google, IBM, Oracle et Ippon (désolé si j'ai oublié votre stand) avaient les stands les plus animés.</p>

<p style="text-align: justify;">Arolla non plus n'a pas manqué le rendez-vous. Deux tiers des consultants Java ont pris part à la manifestation. Deux speakers, <strong>Cyrille Martraire</strong> (Directeur Technique) et <strong>Aude Amarrurtu</strong> (Responsable RH) ont animé les session “<strong>eXtremist Programming</strong>” et “<strong>Des SSII aux SS3I</strong>”, qui ont eu de très bons retours.</p>

Les principaux thèmes au programme de cette année :

<ul>
    <li>Java 8</li>
    <li>Programmation fonctionnelle</li>
    <li>DevOps (avec Docker, Puppet, Chef, Git, Gradle, ... )</li>
    <li>Les process agiles</li>
    <li>Big-Data, NoSQL,</li>
    <li>Divers (Sécurité, Bitcoin, Culture informatique, Education, …)</li>
</ul>

<p style="text-align: justify;">Comme je suis très paresseux, j'ai préféré donner la priorité aux Labs et suivre les Universités plus tard, quand elles seront disponibles sur Parleys. Mais malheureusement les choses ne se sont pas passées comme prévues. Premier jour, première heure, je rate le Lab Docker pour m'être trompé de salle. Je vous laisse donc ici un résumé de ce que j'ai retenu des sessions que j'ai suivies.</p>

<h2><strong>Sessions Java EE 7</strong></h2>

<p style="text-align: justify;">On a eu droit à deux sessions sur Java EE 7. Ça commence par un <a href="https://glassfish.java.net/devoxx/">lab</a> de trois heures le premier jour (<strong>Introduction et mise en pratique de Java EE 7</strong>) pour faire un tour, les mains dans le cambouis, des principales nouveautés de Java EE 7. Au troisième jour de la conférence, Arun Gupta reprend la main dans la session “<strong>50 new things in Java EE 7</strong>” pour une présentation plus élargie des nouveautés.</p>

<p style="text-align: justify;">JEE 7 est la première version sous Oracle depuis le rachat de Java. Les spécifications majeures concernent les <strong>WebSockets</strong> (JSR 356), le support de <strong>JSON</strong> (JSR 353) et les traitements <strong>Batch</strong> (JSR 352). On notera également le support de <strong>HTML5</strong> pour Java Server Faces (JSF) (JSR 352). Des API sont fournies par la plateforme pour chacune de ces spécifications. Pour aller plus loin je vous conseille la lecture de l'<a href="https://blogs.oracle.com/arungupta/entry/java_ee_7_key_features">article</a> d'Arun Gupta.</p>

<p style="text-align: justify;">La première question qu'on se pose quand on découvre JEE7 c'est “<em>Do we really need Spring ?</em>”. La plateforme dispose d'un CDI qui permet de faire de l'injection de dépendance par annotations, sans passer par les fichiers xml qui ont rendu difficile l'adoption de Spring, et en plus elle vient avec certaines implémentations de Spring comme Spring Batch API.</p>

<p style="text-align: justify;">Lors du BOF organisé par le Paris JUG, un débat a été ouvert sur l'intérêt d'utiliser encore Spring dans une application Java EE, avec <strong>Arun Gupta</strong> et <strong>Antonio Goncalvès,</strong> auteurs des livres <em>Java EE 7 Essentials</em> et  <em>Beginning Java EE 7</em>. La plateforme fournit des librairies pour la plupart des fonctionnalités courantes (persistance, sécurité, web service, transaction, traitement batch, ...) d'une application classique. Le seul endroit où ça traîne encore c'est au niveau du moteur de templating, il n y a pas encore de spécification. Spring MVC reste incontournable.</p>

<h2><strong>Sessions Java 8</strong></h2>

<p style="text-align: justify;">L'année 2014 marque un grand tournant pour Java, avec la sortie de la version 8 qui implémente le paradigme fonctionnel tant attendu par les javaistes. C'était la grande vedette de la conférence, avec plus de 5 sessions, et des risques de doublons.</p>

<h2>Tout ce que vous avez voulu savoir sur les lambda :</h2>

Un tour en profondeur du projet lambda avec <strong>Rémi Forax</strong>, jusque dans le bytecode. Je n'ai pas trouvé les slides de la prez. J'avais écrit un billet sur <a href="http://www.arolla.fr/blog/2013/12/java-8-le-projet-lambda-part-2/">les lambda expressions</a>.

<h2>Les concepts de la programmation fonctionnelle illustrés avec java 8 :</h2>

Comment les patterns de base de la PF sont implémentés en Java.

<ul>
<li><strong>First class function</strong></li>
</ul>

[java]
Function&lt;String, String&gt; hello = (String s) -&gt; &quot;hello &quot; + s;
hello.apply(&quot;Erouan&quot;);
[/java]

<ul>
<li><strong>High order function</strong></li>
</ul>

[java]
confs.stream()
	.filter( s -&gt; s.contains(&quot;j&quot;) )
	.collect(Collectors.toList());
[/java]

<ul>
<li><strong>Functor ( ou Mapper)</strong></li>
</ul>

[java]
confs.stream().
	map( s -&gt; s.toUpperCase() )
	.collect( Collectors.toList() );
[/java]

<ul>
<li><strong>Reduce</strong></li>
</ul>

[java]
confs.stream()
	.reduce((s1, s2) -&gt; s1 + &quot;, &quot; + s2 )
	.get();
[/java]

Les slides de la prez sont disponibles <a href="http://www.slideshare.net/YannickChartois/les-concepts-de-la-programmation-fonctionnelle-illustrs-avec-java-8">ici</a>.

<h2>50 nouvelles choses que l'on peut faire avec Java 8 :</h2>

Quand on parle des nouveauté de Java 8 on pense en premier aux lambda expressions. Ensuite viennent les méthodes virtuelles, les interfaces fonctionnelles et les streams. <strong>José Paumard</strong> passe en revue d'autres features non moins intéressantes qui vont nous simplifier le code.

<ul>
<li><strong>Date</strong>
Que ce soit<strong> java.util.Date</strong>, (qui n'est pas une vraie date) ou <strong>java.util.Calendar</strong> (qui n'est pas un vrai calendrier), la manipulation des dates est toujours un casse-tête. Avec l'ajout de nouveaux packages (JSR 310) ça devrait être plus intuitif.</li>
</ul>

<blockquote>The new java.time API consists of 5 packages:
java.time - the base package containing the value objects
java.time.chrono -provides access to different calendar systems
java.time.format - allows date and time to be formatted and parsed
java.time.temporal - the low level framework and extended features
java.time.zone - support classes for time-zones</blockquote>

<a href="http://www.infoq.com/articles/java.time">http://www.infoq.com/articles/java.time</a>

<ul>
<li><strong>Duration (pour la duration) : </strong>une durée (en nanosecondes)</li>
</ul>

[java]
Instant start = Instant.now();
Instant end = Instant.now();
Duration duration = Duration.between(start, end);
[/java]

<ul>
<li><strong>LocalDate :</strong> date tout court (sans heure, ni minute, ni seconde)</li>
</ul>

[java]
LocalDate now = LocalDate.now();
LocalDate birthday = LocalDate.of(1956, Month.APRIL, 23);
[/java]

<ul>
<li><strong>Period :</strong> temps écoulés exemple, "3 years, 2 months and 6 days"</li>
</ul>

[java]
Period period = birthday.until(LocalDate.now());
int year = period.getYear();
[/java]

… voir (TimeZone, Instant, ...)

<ul>
<li><strong>String :</strong> on peut streamer un String avec la nouvelle méthode méthode chars()</li>
</ul>

[java]
IntStream stream = &quot;bonjour&quot;.chars();
stream.forEach(.........);
[/java]

<ul>
<li><strong>List :</strong> avec la nouvelle sort(), on peut effectuer un tri directement sur la liste sans passer par Collections</li>
</ul>

Avant Java 8:

[java]
Collections.sort(people,new Comparator(){...});
[/java]

Avec Java 8 :

[java]
people.sort(new Comparator(){...});
[/java]

Les slides de la prez sont disponibles ici

Les IDE (Idea, Eclipse, Netbeans) sont déjà prêts, reste à savoir si l'adoption de Java 8 par la communauté sera rapide.

<h2><strong>Git ++</strong></h2>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/05/git_logo.png"><img class="size-medium wp-image-2467 alignleft" alt="git_logo" src="http://www.arolla.fr/blog/wp-content/uploads/2014/05/git_logo-300x164.png" width="300" height="164" /></a>Il y a deux choses à retenir dans cette session, comment structurer les messages de commit pour mieux exploiter l'histoire de son repository et la puissance du rebase interactif. Pour la plupart du temps on a tendance à utiliser Git comme on le fait avec le bon vieux SVN, sans prendre le temps de lire la documentation (pourtant très bien fournie). Les commandes git add, git commit et git push suffisent pour ajouter une nouvelle ligne dans le CV. Ce qui fait qu'on passe à côté de choses simples pourtant très utiles qui font la magie de Git.</p>

<p style="text-align: justify;">Une fois la commande <strong><em>git commit</em></strong> lancée, le terminal nous laisse la main avec l'éditeur de notre choix (Vim, pico) pour écrire notre commentaire. La convention de commit proposée est inspirée du projet AngularJS. Le commentaire d'un commit est composé de plusieurs champs :</p>

<address style="text-align: justify; padding-left: 180px;"><span style="color: #800000;">&lt;type&gt;(&lt;scope&gt;): &lt;subject&gt;</span>
<span style="color: #800000;">    &lt;BLANK LINE&gt;</span>
<span style="color: #800000;">&lt;body&gt;</span>
<span style="color: #800000;">    &lt;BLANK LINE&gt;</span>
<span style="color: #800000;">&lt;footer&gt;</span></address>

<ul>
    <li><strong>type :</strong> peut être soit un feature, du refactoring, de la documentation, un fix, …</li>
    <li><strong>scope :</strong> l'endroit des commits (module, couche, projet, … )</li>
    <li><strong>subject :</strong> une description des changements effectués dans le commit</li>
    <li><strong>body :</strong> décrit les motivations du commit</li>
    <li><strong>footer :</strong> les références du commit (Jira, User Strories, ...)</li>
</ul>

<p style="text-align: justify;">Avec le rebase interactif “<strong><em>git rebase -i</em></strong>” on peut effectuer plusieurs actions sur les commits avant de les pusher. Renommer, réordonner, supprimer, fusionner, modifier ou insérer.</p>

<p style="text-align: justify;">A la fin une question a été soulevée plusieurs fois sur les risques de perdre une histoire en modifiant un commit. D'abord les modifs ne concernent que le repo local et non celui qui est partagé, ensuite il faut savoir qu'un commit est immutable, il n'est jamais modifié. . Quand on tente une action de modification sur un commit C Git crée un nouveau commit C' qui est le clone de C + les nouvelles modifications.</p>

<blockquote>If you’ve used Git, you’re probably aware that you can update your latest commit by using <em><span style="color: #008000;">commit --amend</span></em>. But can you really update a commit? In fact, you cannot. Git simply creates a new commit and moves your branch pointer to it. The old value can still be found using <em><span style="color: #008000;">git reflog</span></em>, and can be referred to by its hash value</blockquote>

<a href="http://www.jayway.com/2013/03/03/git-is-a-purely-functional-data-structure/">http://www.jayway.com/2013/03/03/git-is-a-purely-functional-data-structure/</a>

… la suite bientôt!
