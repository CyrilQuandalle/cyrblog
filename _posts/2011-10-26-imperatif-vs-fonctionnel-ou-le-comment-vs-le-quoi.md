---
layout: post
title: Impératif vs. fonctionnel ou le comment vs. le quoi
date: 2011-10-26 16:01
author: nouhoum traore
comments: true
categories: [Fonctionnel, fonctionnel, impératif, scala]
---
<p style="text-align: justify;">Mon intérêt pour Scala m’a conduit à m’intéresser à la programmation fonctionnelle. J’ai eu une expérience plutôt avec des langages privilégiant le style impératif. Scala permettant à la fois les paradigmes fonctionnel et impératif, je me suis donc posé des questions sur les différences fondamentales entre ces deux paradigmes. Je souhaite, dans ce billet, partager avec vous l’une des distinctions que j’ai pu glaner lors mes lectures diverses.
</p>

<h3 style="text-align: justify;">Le cas d'école</h3>
<p style="text-align: justify;">Je vais prendre un exemple qui nous servira de cas d’école. Il s’agira de calculer la somme des éléments d’une liste d’entiers.

</p>

<h3 style="text-align: justify;">Implémentation dans le style impératif</h3>
<p style="text-align: justify;">Voyons une solution implémentée dans le style impératif:</p>

<div style="text-align: justify;">
<div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div>
<div>6</div></td>
<td>
<div>
<div><code>def</code> <code>sum(xs</code><code>:</code> <code>List[Int]) </code><code>=</code> <code>{</code></div>
<div><code>  </code><code>var</code> <code>sum </code><code>=</code> <code>0</code></div>
<div><code>  </code><code>for</code><code>(x &lt;- xs)</code></div>
<div><code>    </code><code>sum +</code><code>=</code> <code>x</code></div>
<div><code>  </code><code>sum</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">En image cela donnne:</p>

<div style="text-align: justify;"><a href="http://4.bp.blogspot.com/-FWMEKOn7a9w/Tj6fhcMyslI/AAAAAAAAApg/LEd6cyUgwrk/s1600/sum_list_imperatif.png"><img src="http://4.bp.blogspot.com/-FWMEKOn7a9w/Tj6fhcMyslI/AAAAAAAAApg/LEd6cyUgwrk/s400/sum_list_imperatif.png" alt="" width="400" height="40" border="0" /></a></div>
<p style="text-align: justify;"></p>
<p style="text-align: justify;"></p>
<p style="text-align: justify;">Quelques remarques à propos de cette première implémentation:</p>

<ul style="text-align: justify;">
	<li>Nous avons effectué plusieurs affections avant d’obtenir le résultat final c’est-à-dire la valeur de la somme des nombres constituant notre liste.</li>
	<li><strong>sum += x</strong> ne retourne pas de valeur mais a pour effet de bord de modifier la valeur contenue dans la variable sum.</li>
	<li>La mutation est au centre de cette version de notre programme: <strong>sum</strong> est une variable mutable c’est-à-dire qui peut changer tout au long de l’exécution du programme. La tâche est accomplie selon un ensemble (une séquence) de commandes: à chaque étape des affectations (modifications d’emplacements mémoire) sont effectuées. Si vous êtes comme moi vous devez être en train de vous dire “Ok ok! Mais je ne vois pas d’ordre ici pourquoi tu me parles de style impératif là?!” Laissez-moi vous guider un peu... L’impératif ici se trouve dans le fait que notre code donne des ordres à l’ordinateur en lui disant à chaque étape quelle valeur il faut affecter à quelle variable : <strong>notre programme dit comment calculer la somme des éléments de notre liste.</strong></li>
</ul>
<p style="text-align: justify;"><strong>En résumé</strong> dans cette première nous itérons sur la liste, séquençons les modifications et faisons des affectations.

</p>

<h3 style="text-align: justify;">Implémentation dans le style fonctionnel</h3>
<p style="text-align: justify;">Voyons maintenant l’implémentation dans le style fonctionnel:</p>

<div style="text-align: justify;">
<div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div></td>
<td>
<div>
<div><code>def</code> <code>sum(xs</code><code>:</code> <code>List[Int])</code><code>:</code> <code>Int </code><code>=</code> <code>xs </code><code>match</code> <code>{</code></div>
<div><code>  </code><code>case</code> <code>Nil </code><code>=</code><code>&gt; </code><code>0</code></div>
<div><code>  </code><code>case</code> <code>head </code><code>::</code> <code>tail </code><code>=</code><code>&gt; head + sum(tail)</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Quelques remarques à propos de cette implémentation:</p>

<ul style="text-align: justify;">
	<li>Pas d’affectation</li>
	<li>La version fonctionnelle consiste à dire à l’ordinateur <strong>ce qu’est la somme</strong> des éléments de la liste; elle est plus déclarative que la version impérative. Et les programmes écrits dans ce style sont en quelque sorte plus simple car nous n’avons pas à raisonner sur les mutations successives des variables.</li>
	<li>La récursion: la récursion est omniprésente en programmation fonctionnelle. Au fur et à mesure je fais de la programmation fonctionnelle, je me rends compte j’en fait très souvent usage pour remplacer notamment les boucles, et exprimer plus simplement (élégamment) certains algorithmes. Mon propos n’est pas de confondre le style fonctionnel avec la récursion mais de souligner un usage plus fréquent de la récursion dans ce style. Ceci n’est pas étranger à la proximité de la programmation fonctionnelle et des mathématiques. Rappelez-vous de la représentation de la <a href="http://fr.wikipedia.org/wiki/Suite_de_Fibonacci">suite de Fibonacci</a> ou de celle d’une <a href="http://fr.wikipedia.org/wiki/Suite_arithm%C3%A9tique">suite arithmétique</a>. L’expression mathématique de ces deux “objets” utilisent la récursion.</li>
</ul>
<p style="text-align: justify;"></p>

<h3 style="text-align: justify;">Quelques mots de conclusion</h3>
<p style="text-align: justify;">Dans cet article je me suis focalisé sur un seul aspect différenciant les paradigmes fonctionnels et impératifs. J’espère revenir dans d’autres billets sur d’autres aspects de la programmation fonctionnelle.
Voici en conclusion quelques points saillants que je retiens de mes récents voyages dans l’univers fonctionnel:</p>

<ul>
	<li style="text-align: justify;">La programmation fonctionnelle est une façon de penser, elle peut se traduire même dans des langages hautement impératifs (qui encouragent le style impératif j’entends). Cependant c’est quand même beaucoup plus facile avec un langage fonctionnel (par exemple Haskell).</li>
	<li style="text-align: justify;">La programmation fonctionnelle n’encourage pas les effets de bord (side effects).</li>
	<li style="text-align: justify;">La pensée fonctionnelle m’aide à réduire la portée de mes variables et les effets de bord, à composer plus souvent des fonctionnalités.</li>
	<li style="text-align: justify;">La programmation fonctionnelle promeut la composabilité.</li>
	<li style="text-align: justify;">La programmation fonctionnelle promeut l’immutabilité et déconseille la mutabilité.</li>
	<li style="text-align: justify;">L’impératif est plus proche du fonctionnement de la machine: une variable est une partie de la mémoire, sa valeur change au fur et à mesure du déroulement du programme. En fonctionnel on privilégie l'usage des valeurs qui sont immuables; pensez aux variables au sens mathématique.</li>
	<li style="text-align: justify;">En impératif les étapes du calcul sont détaillées et l’ordre d’exécution est important.</li>
	<li style="text-align: justify;">En fonctionnel je dis à la machine ce qu’est une opération et je la laisse décider comment la réaliser. Le niveau d’abstraction fait que je n’ai pas besoin de lui préciser toutes les étapes pour qu’elle arrive à accomplir ce que je veux d’elle.</li>
</ul>
