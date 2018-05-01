---
layout: post
title: "Listes Scala: méthodes foldLeft et foldRight"
date: 2011-10-27 12:54
author: nouhoum traore
comments: true
categories: [fold, foldLeft, foldRight, Fonctionnel, fonctionnel, liste, scala]
---
<p style="text-align: justify;">Dans ce billet je souhaite vous parler rapidement des méthodes foldLeft et foldRight de l’API des listes en Scala.</p>

<h3 style="text-align: justify;">foldLeft</h3>
<p style="text-align: justify;"><em>foldLeft</em> est une méthode de la classe <a href="http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List">scala.collection.immutable.List</a> et voici ce que le scaladoc nous en dit:
“<em>Applies a binary operator to a start value and all elements of this list, going left to right.</em>”
Scaladoc nous dit que foldLeft applique une fonction prenant deux paramètres (opérateur binaire) à une valeur initiale (appelée <strong>accumulateur</strong> dans le jargon fonctionnel) et à tous les éléments de la liste en partant de la gauche. Voyons attentivement la signature de la méthode pour rendre les choses un peu plus claires!</p>

<div style="text-align: justify;">
<div id="highlighter_242723">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>foldLeft [B] (z</code><code>:</code> <code>B)(f</code><code>:</code> <code>(B, A) ⇒ B)</code><code>:</code> <code>B</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Ah tiens une chose transparaît dans cette signature: <em>foldLeft</em> est une méthode <a href="http://en.wikipedia.org/wiki/Currying">currifiée</a>. Si vous n’avez jamais fait de programmation fonctionnelle auparavant cette notion ne doit pas vous parler. En fait malgré les apparences la méthode foldLeft ne prend pas deux arguments! Voici comment elle fonctionne: elle prend un paramètre z de type B et renvoie une fonction qui prend à son tour un paramètre qui est une fonction de type (B, A) =&gt; B. z représente la valeur initiale de l’accumulateur et est du même type que la valeur de retour de foldLeft. A chaque étape la fonction f est appliquée à l’accumulateur et à l’élément courant de la liste. La valeur de l’accumulateur peut changer tout au long du déroulement de l’opération. Prenez une minute pour bien visualiser la chose ce n’est pas si compliqué que cela une fois qu’on a compris la notion. La méthode <a href="http://en.wikipedia.org/wiki/Fold_%28higher-order_function%29">fold</a> est d’une puissance incroyable! Elle vous permet de faire quasiment de faire toutes les opérations imaginables avec les listes.</p>
<p style="text-align: justify;">Voici un usage simple de <em>foldLeft</em>: Calculer la somme des éléments d’une liste d’entiers.</p>
L’accumulateur (la valeur initiale) est égal à zéro et l’opérateur binaire est une fonction qui prend deux entiers et fait leur somme:
<div style="text-align: justify;">
<div id="highlighter_300994">
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
<div><code> val</code> <code>nums </code><code>=</code> <code>List(</code><code>1</code><code>, </code><code>2</code><code>, </code><code>3</code><code>, </code><code>4</code><code>)</code></div>
<div><code> val</code> <code>sum </code><code>=</code> <code>nums.foldLeft(</code><code>0</code><code>){</code></div>
<div><code> </code><code>(acc, num) </code><code>=</code><code>&gt; acc + num</code></div>
<div><code>
</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">En action dans le REPL:</p>

<div style="text-align: justify;">
<div id="highlighter_705616">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div>
<div>6</div>
<div>7</div></td>
<td>
<div>
<div><code> scala&gt; </code><code>val</code> <code>nums </code><code>=</code> <code>List(</code><code>1</code><code>, </code><code>2</code><code>, </code><code>3</code><code>, </code><code>4</code><code>)</code></div>
<div><code> nums</code><code>:</code> <code>List[Int] </code><code>=</code> <code>List(</code><code>1</code><code>, </code><code>2</code><code>, </code><code>3</code><code>, </code><code>4</code><code>)</code></div>
<div><code> </code></div>
<div><code> scala&gt; </code><code>val</code> <code>sum </code><code>=</code> <code>nums.foldLeft(</code><code>0</code><code>){</code></div>
<div><code></code><code>(acc, num) </code><code>=</code><code>&gt; acc + num</code></div>
<div><code> }</code></div>
<div><code> sum</code><code>:</code> <code>Int </code><code>=</code> <code>10</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Le déroulé de l’opération foldLeft est le suivant :</p>

<div style="text-align: justify;">
<div id="highlighter_187370">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div></td>
<td>
<div>
<div><code>((((</code><code>0</code> <code>+ </code><code>1</code><code>) + </code><code>2</code><code>) + </code><code>3</code><code>) + </code><code>4</code><code>)</code></div>
<div><code>((((</code><code>1</code><code>) + </code><code>2</code><code>) + </code><code>3</code><code>) + </code><code>4</code><code>)</code></div>
<div><code>(((</code><code>3</code><code>) + </code><code>3</code><code>) + </code><code>4</code><code>)</code></div>
<div><code>((</code><code>6</code><code>) + </code><code>4</code><code>)</code></div>
<div><code>(</code><code>10</code><code>)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">En image cela donne:</p>

<div style="text-align: justify;"><a href="http://1.bp.blogspot.com/-wacs0DzN5JA/TiDuc6rHKVI/AAAAAAAAAnc/JbjLky1t57s/s1600/foldLeft.png"><img src="http://1.bp.blogspot.com/-wacs0DzN5JA/TiDuc6rHKVI/AAAAAAAAAnc/JbjLky1t57s/s400/foldLeft.png" alt="" width="400" height="300" border="0" /></a></div>
<h3 style="text-align: justify;"></h3>
<h3 style="text-align: justify;"></h3>
&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;
<h3 style="text-align: justify;"></h3>
&nbsp;

&nbsp;

&nbsp;

&nbsp;
<h3 style="text-align: justify;">foldRight</h3>
<p style="text-align: justify;"><em>foldRight</em> est assez similaire à <em>foldLeft</em> mais il parcourt la liste en partant de la droite de la liste. Ainsi c’est le dernier élément de la liste qui est d’abord traité. Voyons l’implémentation de la somme des éléments d’une liste avec <em>foldRight</em>:</p>

<div style="text-align: justify;">
<div id="highlighter_17812">
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
<div><code>scala&gt; </code><code>val</code> <code>nums </code><code>=</code> <code>List(</code><code>1</code><code>, </code><code>2</code><code>, </code><code>3</code><code>, </code><code>4</code><code>)</code></div>
<div><code>nums</code><code>:</code> <code>List[Int] </code><code>=</code> <code>List(</code><code>1</code><code>, </code><code>2</code><code>, </code><code>3</code><code>, </code><code>4</code><code>)</code></div>
<div><code>scala&gt; </code><code>val</code> <code>sum </code><code>=</code> <code>nums.foldRight(</code><code>0</code><code>) {(num, acc) </code><code>=</code><code>&gt;</code></div>
<div><code> </code><code>num + acc</code></div>
<div><code>}</code></div>
<div><code>sum</code><code>:</code> <code>Int </code><code>=</code> <code>10</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Le déroulé de l’opération foldLeft est le suivant :</p>

<div style="text-align: justify;">
<div id="highlighter_762822">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div></td>
<td>
<div>
<div><code>(</code><code>1</code> <code>+ (</code><code>2</code> <code>+ (</code><code>3</code> <code>+ (</code><code>4</code> <code>+ </code><code>0</code><code>))))</code></div>
<div><code>(</code><code>1</code> <code>+ (</code><code>2</code> <code>+ (</code><code>3</code> <code>+ (</code><code>4</code><code>))))</code></div>
<div><code>(</code><code>1</code> <code>+ (</code><code>2</code> <code>+ (</code><code>7</code><code>)))</code></div>
<div><code>(</code><code>1</code> <code>+ (</code><code>9</code><code>))</code></div>
<div><code>(</code><code>10</code><code>)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h4 style="text-align: justify;">Conclusion</h4>
<p style="text-align: justify;">Prenez le temps de digérer ce contenu, si jamais l’envie me prend dans l’avenir je reviendrais sur une comparaison plus avancée entre ces deux méthodes.</p>
