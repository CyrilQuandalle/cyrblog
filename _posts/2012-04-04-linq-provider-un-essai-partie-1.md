---
layout: post
title: "Linq provider : un essai… partie 1"
date: 2012-04-04 12:23
author: pierre irrmann
comments: true
categories: [AST, C#, LINQ, Outils, Programmation]
---
<h3 style="text-align: justify;"><span style="color: #333333;">L’appel des AST</span></h3>

<p style="text-align: justify;">J’ai depuis assez longtemps envie de m’essayer à l’implémentation d’un provider LINQ basique. L’idée générale est de me frotter un peu à la manipulation d’AST (Abstract Syntax Tree, Arbre syntaxique abstrait), en utilisant pour cela mon langage de prédilection : C#. J’ai également une certaine tentation de réaliser cet essai en F#, langage extrêmement bien adapté à la manipulation d’arbres syntaxiques, mais je ne me sens pas encore prêt… peut-être plus tard !</p>

<p style="text-align: justify;">Pour préparer cette implémentation, qui va se baser sur l’interface IQueryable, je vais tout d’abord citer une référence en la matière, en traduisant un article de la série EDULINQ de <a href="http://stackoverflow.com/users/22656/jon-skeet" target="_blank">Jon Skeet</a> : Out-of-process queries with IQueryable.</p>

<p style="text-align: justify;">Si vous ne connaissez pas Jon Skeet, il est un peu le Chuck Norris de la programmation. Peut-être que <a href="http://meta.stackoverflow.com/questions/9134/jon-skeet-facts" target="_blank">certains faits à son propos</a> pourrons vous éclairer…</p>

<p style="text-align: justify;">Personnellement, j’adore celui-ci :</p>

<blockquote>Jon Skeet has already written a book about C# 5.0.

It’s currently sealed up.

In three years, <a href="http://en.wikipedia.org/wiki/Anders_Hejlsberg">Anders Hejlsberg</a> is going to open the book to see if the language design team got it right.</blockquote>

<p style="text-align: justify;">Concernant la traduction de l’article sur IQueryable, c’est ici : <a href="http://www.arolla.fr/blog/2012/04/requetes-hors-processus-avec-iqueryable-traduction-de-l-article-de-jon-skeet/" target="_blank">Requêtes hors-processus avec IQueryable</a>.</p>

<p style="text-align: justify;">Pour suivre l’implémentation du provider LINQ, c’est… <a href="http://www.arolla.fr/blog/2012/06/linq-provider-un-essai%E2%80%A6-partie-2/" target="_blank">ici</a>.</p>
