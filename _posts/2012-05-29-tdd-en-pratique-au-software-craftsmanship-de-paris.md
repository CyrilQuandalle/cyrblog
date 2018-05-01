---
layout: post
title: TDD en pratique au Software Craftsmanship de Paris
date: 2012-05-29 14:58
author: nouhoum traore
comments: true
categories: [Bonnes pratiques de dév, Evénements, Programmation, software craftsmanship, TDD]
---
<p style="text-align: justify;">Jeudi 24 mai, il est 20 heures, une trentaine de passionnés du développement, des craftsmen et des aspirants craftsmen, des maîtres du TDD et ceux qui souhaitent s’y essayer se retrouvent près de Bastille. La soirée va bientôt commencer, les participants ont pris des forces grâce au buffet bien garni offert par <a title="Site web d'Arolla" href="http://www.arolla.fr/">Arolla</a>, sponsor de la soirée. La vedette de la soirée n’est autre que le “TDD en pratique”, et Cyrille (@cyriux) en maître de cérémonie fera tout pour faire briller les yeux des passionnés.</p>

<p style="text-align: justify;">La soirée va bientôt commencer. Tout le monde prend place. Cyrille présente la <a href="http://www.meetup.com/paris-software-craftsmanship/">Communauté Software Craftsmanship de Paris</a>, le thème et le déroulement de la soirée. Nous commençons par deux lightning talks : le premier sur <a href="https://github.com/alexruiz/fest-assert-2.x/wiki">Fest Assert</a>, une librairie Java qui permet d’écrire des assertions ‘fluent’ par <a href="https://github.com/joel-costigliola">Joël Costigliola</a> et le second sur les <a href="http://www.jbrains.ca/permalink/the-four-elements-of-simple-design" target="_blank">“quatre éléments d’un design simple”</a> par Cyrille.</p>

<h2 style="text-align: justify;">Fest assert</h2>

<p style="text-align: justify;">Joel Costigliola, contributeur au projet, nous présente cette librairie dont le but est de renforcer la lisibilité de vos tests unitaires. L’outil s’intègre avec les deux frameworks de test unitaire, JUnit et TestNG. Voyons quelques exemples pour vous donner envie d’en savoir. Pour ce faire commençons par créer une petite classe qui nous servira dans les illustrations :</p>

[java]
package org.craft.tdd.fest;

public class Person {
	private int age;
	private String name;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String name() {
		return name;
	}

	public int age() {
		return age;
	}
}
[/java]

<p style="text-align: justify;">Ajoutez Fest Assert aux dépendances de votre projet et ajoutez l’import static suivant dans votre classe de test et vous êtes prêt pour démarrer :</p>

[java]
import static org.fest.assertions.api.Assertions.*;
[/java]

<p style="text-align: justify;">Voici des tests simples sur les propriétés de notre classe. Jugez vous-mêmes de leur expressivité :</p>

[java]
Person craftsman = new Person(&amp;amp;amp;quot;John Doe&amp;amp;amp;quot;, 22);
Person craftswoman = new Person(&amp;amp;amp;quot;Jane Doe&amp;amp;amp;quot;, 20);

assertThat(craftsman.name()).isEqualTo(&amp;amp;amp;quot;John Doe&amp;amp;amp;quot;);
assertThat(craftsman.name()).isNotEqualTo(&amp;amp;amp;quot;Jackson Doe&amp;amp;amp;quot;);
assertThat(craftsman.age()).as(&amp;amp;amp;quot;The craftsman should be major.&amp;amp;amp;quot;).isGreaterThan(18);
[/java]

<p style="text-align: justify;">L’API de fest assert permet le chaînage. Cela donne par exemple :</p>

[java]
assertThat(craftsman.name()).isEqualTo(&amp;amp;amp;quot;John Doe&amp;amp;amp;quot;)
				 .isNotEqualTo(&amp;amp;amp;quot;Jackson Doe&amp;amp;amp;quot;)
				 .contains(&amp;amp;amp;quot;John&amp;amp;amp;quot;)
				 .containsIgnoringCase(&amp;amp;amp;quot;DOE&amp;amp;amp;quot;)
				 .hasSize(8);
[/java]

<p style="text-align: justify;">Vous avez besoin de tester si une valeur est ou n’est pas dans une liste Fest a une API pour cela. Notez en passant l’utilisation de la méthode utilitaire pour la création d’une liste :</p>

[java]
import static org.fest.assertions.api.Assertions.*;
import static org.fest.util.Collections.list;
...
List&amp;amp;amp;lt;String&amp;amp;amp;gt; names = list(&amp;amp;amp;quot;Linus Torvalds&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Odersky&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Fowler&amp;amp;amp;quot;, &amp;amp;amp;quot;John
Doe&amp;amp;amp;quot;);

assertThat(craftsman.name()).isIn(names);
assertThat(craftsman.name()).isNotIn(&amp;amp;amp;quot;Otis Redding&amp;amp;amp;quot;, &amp;amp;amp;quot;Sam Cook&amp;amp;amp;quot;, &amp;amp;amp;quot;Ray Charles&amp;amp;amp;quot;);
[/java]

<p style="text-align: justify;">Enfin voici pour finir quelques tests sur les collections :</p>

[java]
import static org.fest.assertions.api.Assertions.*;
import static org.fest.util.Collections.list;
...
List&amp;amp;amp;lt;String&amp;amp;amp;gt; names = list(&amp;amp;amp;quot;Linus Torvalds&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Odersky&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Fowler&amp;amp;amp;quot;, &amp;amp;amp;quot;John Doe&amp;amp;amp;quot;);
List&amp;amp;amp;lt;String&amp;amp;amp;gt; names2 = list(&amp;amp;amp;quot;Linus Torvalds&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Odersky&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Fowler&amp;amp;amp;quot;, &amp;amp;amp;quot;John Doe&amp;amp;amp;quot;);
List&amp;amp;amp;lt;String&amp;amp;amp;gt; names3 = list(&amp;amp;amp;quot;Linus Torvalds&amp;amp;amp;quot;, &amp;amp;amp;quot;Martin Odersky&amp;amp;amp;quot;, &amp;amp;amp;quot;Rob Pike&amp;amp;amp;quot;, &amp;amp;amp;quot;Dennis Ritchie&amp;amp;amp;quot;);

assertThat(names).hasSize(4)
		 .contains(&amp;amp;amp;quot;Martin Odersky&amp;amp;amp;quot;)
		 .doesNotHaveDuplicates()
		 .isNotEmpty()
		 .isEqualTo(names2)
		 .isNotEqualTo(names3);
[/java]

<p style="text-align: justify;">J’espère que cela vous a donné envie d’essayer Fest Assert dans votre projet !</p>

<h2 style="text-align: justify;">Les quatre éléments d’un design simple</h2>

<p style="text-align: justify;">Le deuxième lightning talk parlait de design : il résumait un design simple en quatre éléments, du plus important au moins important :</p>

<ol style="text-align: justify;">
    <li>Il passe ses tests</li>
    <li>Il minimise la duplication</li>
    <li>Il maximise la clarté</li>
    <li>Il a peu d’éléments (it has fewer elements)</li>
</ol>

<p style="text-align: justify;">Comme le faisait remarquer Cyrille, malgré son apparente simplicité, cette liste est puissante. Pour vous en convaincre lisez <a href="http://www.jbrains.ca/permalink/the-four-elements-of-simple-design" target="_blank">l'article</a> dont sont issus les quatre éléments. J’ai notamment aimé la démarche de l’auteur pour refactorer son code !</p>

<h2 style="text-align: justify;">TDD en pratique</h2>

<p style="text-align: justify;">Les lightning talks n’étaient qu’une mise en bouche. Le vrai sujet de la soirée était le TDD en pratique. Après les lightning talks, les craftsmen et craftswomen se mettent en binôme pour travailler sur le kata ayant recueilli le plus de votes, un grand classique : Le Kata "Roman Numerals". L’exercice va se dérouler en trois sessions d’une durée de 30 min environ. A la fin de chaque session, chacun change de binôme et recommence l’exercice. Cyrille et Houssam (<a href="http://twitter.com/#!/houssamfakih">@houssamfakih</a>) jouent le rôle de facilitateurs.</p>

<p style="text-align: justify;">Lors de la dernière session de 30 minutes Cyrille et son binôme <a href="http://twitter.com/#!/piwai">@piwai</a> ont projeté ce qu’ils faisaient sur l’écran. Cela a permis aux autres développeurs d'observer le kata et d’échanger. C’était assez intéressant de voir comment le code évoluait. A certains moments on voyait visuellement des répétitions dans le code et évidemment le refactoring suivait.</p>

<p style="text-align: justify;">Personnellement j’ai beaucoup aimé regarder les autres travailler. Cela me pousse à réfléchir sur ma propre façon d’aborder les problèmes. J’ai aussi constaté qu’il est parfois difficile de résister à la tentation de spéculer, d’aller trop vite et de laisser les tests “driver” le développement en suivant les étapes suivantes [<a href="http://www.amazon.fr/Test-Driven-Development-By-Example/dp/0321146530">Kent Beck</a>] :</p>

<ol style="text-align: justify;">
    <li>Ajouter un test</li>
    <li>Exécuter les tests (les tests qu’on vient d’écrire pourrait échouer)</li>
    <li>Ne faire que le changement minimum nécessaire pour faire passer le test</li>
    <li>Exécuter les tests (les tests devraient réussir)</li>
    <li>Refactorer le code pour supprimer les duplications</li>
</ol>

<p style="text-align: justify;">Il est essentiel de résister à l'envie d'aller trop vite et d'être trop intelligent (clever) afin de ne pas perdre les <a href="http://en.wikipedia.org/wiki/Test-driven_development">intérêts du TDD</a>. Un autre point important abordé lors du kata était le choix du prochain test à écrire. Quel prochain test est le plus intéressant à choisir ? Sans doute celui qui va nous apprendre le plus !</p>

<h2 style="text-align: justify;">Et pour la route</h2>

<p style="text-align: justify;">Cette session a été l’occasion de pratiquer le TDD, de rencontrer du beau monde, de découvrir pour certains de nouveaux outils (MoreUnit, <a href="https://github.com/alexruiz/fest-assert-2.x">Fest Assert</a>, MouseFeed, <a href="http://infinitest.github.com/">Infinitest</a>). Une soirée bien sympathique en somme ! A bientôt, peut-être à la prochaine soirée de la <a href="http://www.meetup.com/paris-software-craftsmanship/">Communauté Software Craftsmanship de Paris</a>.</p>

&nbsp;
&nbsp;
&nbsp;

<p style="text-align: justify;">Quelques liens pour en savoir plus:</p>

<a href="https://fr.wikipedia.org/wiki/Software_craftsmanship" target="_blank">https://fr.wikipedia.org/wiki/Software_craftsmanship</a>

<a href="http://manifesto.softwarecraftsmanship.org/" target="_blank">http://manifesto.softwarecraftsmanship.org/</a>

<a href="http://www.meetup.com/fr/paris-software-craftsmanship/" target="_blank">http://www.meetup.com/fr/paris-software-craftsmanship/</a>

<a href="http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862" target="_blank">http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862</a>
