---
layout: post
title: La gestion des erreurs avec scala.util.Try (1/2)
date: 2012-09-13 11:27
author: nouhoum traore
comments: true
categories: [Error handling, Fonctionnel, Programmation, programming, scala]
---
<p style="text-align: justify;">Scala 2.10 introduit une nouvelle structure monadique Try. Cette classe est particulièrement adaptée à la représentation d'un traitement pouvant réussir en renvoyant une valeur ou échouer en lançant une exception. Commerçons par un exemple simple : parser un numéro de port renseigné par un utilisateur sous forme d'une chaîne de caractères.</p>

<div style="text-align: justify;">
<div id="highlighter_6713">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div></td>
<td>
<div>
<div><code>val</code> <code>input</code><code>:</code> <code>String </code><code>=</code> <code>""</code></div>
<div><code>val</code> <code>port </code><code>=</code> <code>input.toInt</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Ce code est assez simple mais il n'est pas sans danger. En effet si l'utilisateur rentre une valeur qui n'est pas un entier la deuxième ligne du code ci-dessus lance une exception de type NumberFormatException. Voyons cela en action :</p>

<div style="text-align: justify;">
<div id="highlighter_722526">
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
<div><code>scala&gt;</code><code>val</code> <code>input </code><code>=</code> <code>"8080"</code></div>
<div><code>input</code><code>:</code> <code>String </code><code>=</code> <code>8080</code></div>
<div><code>scala&gt;input.toInt</code></div>
<div><code>res</code><code>0</code><code>:</code> <code>Int </code><code>=</code> <code>8080</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Avec un input qui n'est pas un nombre :</p>

<div style="text-align: justify;">
<div id="highlighter_318410">
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
<div><code>scala&gt;</code><code>val</code> <code>input </code><code>=</code> <code>"toto"</code></div>
<div><code>input</code><code>:</code> <code>String </code><code>=</code> <code>toto</code></div>
<div><code>scala&gt;input.toInt</code></div>
<div><code>java.lang.NumberFormatException</code><code>:</code> <code>For input string</code><code>:</code> <code>"toto"</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Maintenant encapsulons le parsing dans un objet Try.</p>

<div style="text-align: justify;">
<div id="highlighter_598923">
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
<div><code>scala&gt;</code><code>import</code> <code>scala.util.Try</code></div>
<div><code>import</code> <code>scala.util.Try</code></div>
<div><code>scala&gt;</code><code>val</code> <code>input </code><code>=</code> <code>"8080"</code></div>
<div><code>input</code><code>:</code> <code>String </code><code>=</code> <code>8080</code></div>
<div><code>scala&gt;Try(input.toInt)</code></div>
<div><code>res</code><code>1</code><code>:</code> <code>scala.util.Try[Int] </code><code>=</code> <code>Success(</code><code>8080</code><code>)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Le résultat est une instance de scala.util.Success, l'une des deux classes concrètes dérivées de Try. La méthode get permet de récupérer la valeur.</p>

<div style="text-align: justify;">
<div id="highlighter_405338">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div></td>
<td>
<div>
<div><code>scala&gt;res</code><code>1</code><code>.get</code></div>
<div><code>res</code><code>3</code><code>:</code> <code>Int </code><code>=</code> <code>8080</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Que se passe-t-il si une erreur se produit lors du parsing de la chaîne ?</p>

<div style="text-align: justify;">
<div id="highlighter_446758">
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
<div><code>scala&gt;</code><code>val</code> <code>input </code><code>=</code> <code>"toto"</code></div>
<div><code>input</code><code>:</code> <code>String </code><code>=</code> <code>toto</code></div>
<div><code>scala&gt;Try(input.toInt)</code></div>
<div><code>res</code><code>4</code><code>:</code> <code>scala.util.Try[Int]</code><code>=</code><code>Failure(java.lang.NumberFormatException</code><code>:</code> <code>For input string</code><code>:</code> <code>"toto"</code><code>)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Cette fois-ci le résultat est une instance de scala.util.Failure, l'autre classe concrète dérivée de Try. Il contient l'exception lancée suite au traitement. L'invocation de la méthode get sur une instance de Failure lance l'exception qu'elle contient. Essayons-le :</p>

<div style="text-align: justify;">
<div id="highlighter_639571">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div></td>
<td>
<div>
<div><code>scala&gt;res</code><code>4</code><code>.get</code></div>
<div><code>java.lang.NumberFormatException</code><code>:</code> <code>For input string</code><code>:</code> <code>"toto"</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Il existe une méthode qui permet de spécifier une valeur par défaut qui est renvoyée en cas d'échec du traitement.</p>

<div style="text-align: justify;">
<div id="highlighter_535504">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div></td>
<td>
<div>
<div><code>scala&gt;res</code><code>4</code><code>.getOrElse(</code><code>80</code><code>)</code></div>
<div><code>res</code><code>7</code><code>:</code> <code>Int </code><code>=</code> <code>80</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;"><a title="La gestion des erreurs avec scala.util.Try (2/2)" href="http://www.arolla.fr/blog/2012/10/la-gestion-des-erreurs-avec-scala-util-try-22/" target="_blank">Voici pour les bases avant de voir dans la deuxième partie des exemples plus avancés. Stay tuned !</a></p>
