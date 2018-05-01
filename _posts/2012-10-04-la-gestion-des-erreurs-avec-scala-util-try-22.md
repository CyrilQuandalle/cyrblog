---
layout: post
title: La gestion des erreurs avec scala.util.Try (2/2)
date: 2012-10-04 14:22
author: nouhoum traore
comments: true
categories: [Error handling, Fonctionnel, Functional, Programmation, programming, scala]
---
<a title="La gestion des erreurs avec scala.util.Try (1/2)" href="http://www.arolla.fr/blog/2012/09/la-gestion-des-erreurs-avec-scala-util-try-12/" target="_blank">Suite de l'épisode précédent.</a>
<p style="text-align: justify;">Après la découverte des bases de la gestion des erreurs avec scala.util.Try, nous allons, dans cette deuxième partie, explorer des fonctionnalités plus avancées. Nous utiliserons un exemple très simple pour illustrer nos propos : lancer un serveur sur un numéro de port fourni par l'utilisateur. Cette tâche se décompose en deux sous-tâches, la première consiste à convertir l'entrée (input dans le code) de l'utilisateur en un numéro de port et la seconde à démarrer le serveur sur ce port. Commençons par la première :</p>

<div style="text-align: justify;">
<div id="highlighter_38241">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div>
<div>2</div></td>
<td>
<div>
<div><code>val</code> <code>input </code><code>=</code> <code>"..."</code></div>
<div><code>val</code> <code>result </code><code>=</code> <code>Try { input.toInt }</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Encapsulons ce bout de code dans une fonction <em>parsePort</em>:</p>

<div style="text-align: justify;">
<div id="highlighter_240613">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>parsePort(input</code><code>:</code> <code>String)</code><code>:</code> <code>Try[Int] </code><code>=</code> <code>Try(input.toInt)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Comme nous l'avons vu dans la première partie le type de retour de cette méthode est scala.util.Success ou scala.util.Failureselon que la valeur saisie par l'utilisateur soit un nombre ou non. La méthode ci-dessous teste notre super méthode :</p>

<div style="text-align: justify;">
<div id="highlighter_661471">
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
<div><code>@</code><code>Test</code></div>
<div><code>def</code> <code>parsePortTest() {</code></div>
<div><code> </code><code>assertTrue(parsePort(</code><code>"80"</code><code>).isInstanceOf[Success[</code><code>_</code><code>]])</code></div>
<div><code> </code><code>assertTrue(parsePort(</code><code>"toto"</code><code>).isInstanceOf[Failure[</code><code>_</code><code>]])</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Munis de cette méthode attaquons-nous à la deuxième sous-tâche : la transformation du numéro de port en un serveur sur ce port.</p>

<h2 style="text-align: justify;">Transformer la valeur contenue dans Try</h2>
<p style="text-align: justify;">La méthode transform correspond à notre problématique. Voici sa signature :</p>

<div style="text-align: justify;">
<div id="highlighter_642735">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>transform[U](s</code><code>:</code> <code>(T) ⇒ Try[U], f</code><code>:</code> <code>(Throwable) </code><code>=</code><code>&gt; Try[U])</code><code>:</code> <code>Try[U]</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">C'est une fonction d'ordre supérieur qui prend deux paramètres :</p>

<ul style="text-align: justify;">
	<li>une fonction <em>s</em>qui s'applique lorsque le traitement dans Try a réussi. Dans notre cas cette fonction est définie comme suit :
<div>
<div id="highlighter_502267">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>handleSuccess </code><code>=</code> <code>(port</code><code>:</code> <code>Int) ⇒ Try(SimpleServer(port))</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
	<li>une fonction f qui s'applique lorsque le traitement a échoué. Nous utiliserons 80 comme numéro de port par défaut en cas d'erreur. Voici comment on la définit :
<div>
<div id="highlighter_658467">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>handleFailure </code><code>=</code> <code>(t</code><code>:</code> <code>Throwable) ⇒ Try(SimpleServer(</code><code>80</code><code>))</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
</ul>
<p style="text-align: justify;">Le type SimpleServer ci-dessus est :</p>

<div style="text-align: justify;">
<div id="highlighter_263246">
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
<div><code>case</code> <code>class</code> <code>SimpleServer(port</code><code>:</code> <code>Int) {</code></div>
<div><code> </code><code>def</code> <code>start()</code><code>:</code> <code>Unit </code><code>=</code> <code>{</code></div>
<div><code>   </code><code>println(</code><code>"Starting the server on port "</code> <code>+ port)</code></div>
<div><code>   </code><code>...</code></div>
<div><code> </code><code>}</code></div>
<div><code> </code><code>...</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Et enfin le test du code :</p>

<div style="text-align: justify;">
<div id="highlighter_65130">
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
<div>7</div>
<div>8</div>
<div>9</div>
<div>10</div>
<div>11</div>
<div>12</div>
<div>13</div>
<div>14</div>
<div>15</div>
<div>16</div>
<div>17</div>
<div>18</div></td>
<td>
<div>
<div><code>import</code> <code>org.junit.Test</code></div>
<div><code>import</code> <code>org.junit.Assert.</code><code>_</code></div>
<div><code>import</code> <code>scala.util.{Failure, Success, Try}</code></div>
<div><code>@</code><code>Test</code></div>
<div><code>def</code> <code>transformTest() {</code></div>
<div><code>  </code><code>//Transform the result</code></div>
<div><code> </code><code>def</code> <code>handleSuccess </code><code>=</code> <code>(port</code><code>:</code> <code>Int) ⇒ Try(SimpleServer(port))</code></div>
<div><code> </code><code>def</code> <code>handleFailure </code><code>=</code> <code>(t</code><code>:</code> <code>Throwable) ⇒ Try(SimpleServer(</code><code>80</code><code>))</code></div>
<div><code> </code><code>//Normal case : we create the server instance with the specified port</code></div>
<div><code> </code><code>val</code> <code>normalCase </code><code>=</code> <code>parsePort(</code><code>"9090"</code><code>).transform(handleSuccess, handleFailure)</code></div>
<div><code> </code><code>assertEquals(Success(SimpleServer(</code><code>9090</code><code>)), normalCase)</code></div>
<div><code> </code><code>//Erroneous case : we create the server instance with the default port</code></div>
<div><code> </code><code>val</code> <code>erroneousCase </code><code>=</code> <code>parsePort(</code><code>"toto"</code><code>) transform (handleSuccess, handleFailure)</code></div>
<div><code> </code><code>assertEquals(Success(SimpleServer(</code><code>80</code><code>)), erroneousCase)</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h2 style="text-align: justify;">Fournir un traitement par défaut en cas d'échec</h2>
<p style="text-align: justify;">La méthode <em>recover</em> permet de spécifier une fonction dont l'invocation fournit une valeur dans le cas où l'instance du Try est un objet scala.util.Failure.</p>

<div style="text-align: justify;">
<div id="highlighter_644737">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>recover[U &gt;</code><code>:</code> <code>T](f</code><code>:</code> <code>PartialFunction[Throwable, U])</code><code>:</code> <code>Try[U]</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Vous aurez remarqué que la fonction en question est une fonction partielle, et nous la définissons comme suit en ne prenant en compte que les exceptions de type <em>NumberFormatException</em>. Lorsqu'une telle exception est lancée nous utilisons le port 80.</p>

<div style="text-align: justify;">
<div id="highlighter_201340">
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
<div>7</div>
<div>8</div>
<div>9</div>
<div>10</div>
<div>11</div>
<div>12</div>
<div>13</div>
<div>14</div>
<div>15</div>
<div>16</div></td>
<td>
<div>
<div><code>import</code> <code>org.junit.Test</code></div>
<div><code>import</code> <code>org.junit.Assert.</code><code>_</code></div>
<div><code>import</code> <code>scala.util.{ Failure, Success, Try }</code></div>
<div><code>@</code><code>Test</code></div>
<div><code>def</code> <code>recoverTest() {</code></div>
<div><code> </code><code>val</code> <code>erroneousCase </code><code>=</code> <code>parsePort {</code></div>
<div><code>   </code><code>"toto"</code></div>
<div><code> </code><code>} recover {</code></div>
<div><code>   </code><code>case</code> <code>_:</code> <code>NumberFormatException ⇒ </code><code>80</code> <code>// PartialFunction</code></div>
<div><code> </code><code>} flatMap { port ⇒</code></div>
<div><code>   </code><code>Success(SimpleServer(port))</code></div>
<div><code> </code><code>}</code></div>
<div><code> </code><code>assertEquals(Success(SimpleServer(</code><code>80</code><code>)), erroneousCase)</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">So far so good ! Maintenant voyons comment appliquer un prédicat sur le contenu de Try.</p>

<h2 style="text-align: justify;">Filtrer sur le contenu dans une instance de Try</h2>
<p style="text-align: justify;">Après avoir parsé le numéro renseigné par l'utilisateur nous devons d'abord vérifier qu'il n'est pas déjà utilisé par un autre service. La méthode filter sert à cela :</p>

<div style="text-align: justify;">
<div id="highlighter_276581">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>parsePort (</code><code>"8080"</code><code>)</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Nous filtrons le résultat obtenu avec la méthode filter de Try et le prédicat suivant :</p>

<div style="text-align: justify;">
<div id="highlighter_319119">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>predicate(port</code><code>:</code> <code>Int)</code><code>:</code> <code>Boolean </code><code>=</code> <code>port !</code><code>=</code> <code>8080</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">La méthode filter a la signature suivante :</p>

<div style="text-align: justify;">
<div id="highlighter_194578">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>1</div></td>
<td>
<div>
<div><code>def</code> <code>filter(p</code><code>:</code> <code>(T) ⇒ Boolean)</code><code>:</code> <code>Try[T]</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Elle prend un prédicat et transforme le Try en un objet de type Failure si le prédicat n'est pas satisfait. FilterTest résume tout cela :</p>

<div style="text-align: justify;">
<div id="highlighter_784649">
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
<div>7</div>
<div>8</div>
<div>9</div></td>
<td>
<div>
<div><code>@</code><code>Test</code></div>
<div><code>def</code> <code>filterTest() {</code></div>
<div><code>  </code><code>val</code> <code>erroneousCase </code><code>=</code> <code>parsePort {</code></div>
<div><code>    </code><code>"8080"</code></div>
<div><code>  </code><code>} filter {</code></div>
<div><code>    </code><code>predicate(</code><code>_</code><code>)</code></div>
<div><code>  </code><code>}</code></div>
<div><code>  </code><code>assertTrue(erroneousCase.isInstanceOf[Failure[</code><code>_</code><code>]])</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">No restons pas sur cet échec car la méthode <em>recover</em>est là pour permettre de spécifier une valeur par défaut, par exemple 8081, dans le cas où le filtrage échoue.</p>

<div style="text-align: justify;">
<div id="highlighter_28613">
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
<div>7</div>
<div>8</div>
<div>9</div>
<div>10</div>
<div>11</div>
<div>12</div>
<div>13</div></td>
<td>
<div>
<div><code>@</code><code>Test</code></div>
<div><code>def</code> <code>filterTest() {</code></div>
<div><code>  </code><code>val</code> <code>result </code><code>=</code> <code>parsePort {</code></div>
<div><code>    </code><code>"8080"</code></div>
<div><code>  </code><code>} filter {</code></div>
<div><code>    </code><code>predicate </code><code>_</code></div>
<div><code>  </code><code>} recover {</code></div>
<div><code>    </code><code>case</code> <code>_</code> <code>⇒ </code><code>8081</code></div>
<div><code>  </code><code>} map { port ⇒</code></div>
<div><code>    </code><code>SimpleServer(port)</code></div>
<div><code>  </code><code>}</code></div>
<div><code>  </code><code>assertEquals(Success(SimpleServer(</code><code>8081</code><code>)), result)</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Ce code fait beaucoup de choses. D'abord il parse la chaine "8080" et obtient <em>Success(8080)</em>. Ensuite il transforme cet objet en un objet <em>Failure</em> avec la méthode <em>filter</em> car le prédicat n'est pas satisfait. La méthode <em>recover</em> fournit 8081 comme valeur par défaut et enfin <em>map</em>crée le serveur avec ce port.</p>

<h2 style="text-align: justify;">Et pour la route</h2>
<p style="text-align: justify;">Ici prend fin notre découverte de <em>scala.util.Try</em> qui offre une API élégante pour gérer des traitements pouvant échouer. À ce point vous en savez assez pour aller plus loin.</p>
