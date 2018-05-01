---
layout: post
title: JavaScript et le hoisting
date: 2015-07-06 10:23
author: didier cauvin
comments: true
categories: [hoisting, javascript, js, Programmation]
---
Dans cet article, je souhaiterais évoquer avec vous le 'hoisting'. Je vais tenter d'expliquer comment cela se passe dans les coulisses de JavaScript lorsqu'on déclare ou définit une fonction ou une variable. Rien n'est laissé au hasard, et finalement, ce qui pouvait sembler être une bizarrerie liée au langage s'explique très facilement en quelques lignes de code.

<h1>Définition</h1>

D'après WordReference, le verbe anglais <i>to hoist</i> signifie : remonter, élever, hisser. Eh bien, c'est ce qui se passe avec JavaScript. Avant d'être exécuté, l'interpréteur va remonter toutes les déclarations de variables et de fonctions tout en haut de leur contexte d'exécution.

Il existe deux types de contexte :

<ul>
    <li>La fonction dans laquelle la déclaration a été faite</li>
    <li>Le contexte d'exécution globale si la déclaration a été faite en dehors de toute fonction</li>
</ul>

Les exemples ci-dessous vous aideront à mieux comprendre ce mécanisme de remontée.

<h1>Oh hisse!</h1>

<h2>Les variables</h2>

D'après vous, que retourne le code suivant?

<code>console.log(a);
var a = 2;</code>

Il faut bien différencier déclaration et affectation. Pourquoi? Chaque déclaration de variable est placée tout en haut de sa portée par l'interpréteur JavaScript. Dans l'exemple ci-dessus, la portée se trouve dans le contexte d'exécution global, car la variable n'est déclarée dans aucune fonction. Ainsi, l'interpréteur va remonter la déclaration de la variable avant le reste. Cela revient à écrire ceci :

<code>var a;
console.log(a);
a = 2;</code>

Ainsi, on comprend donc mieux maintenant pourquoi le résultat affiché est <code>undefined</code>.

On aurait aussi très bien pu écrire le code suivant pour avoir le résultat attendu :

<code>a = 1;
console.log(a);
var a;</code>

Ici, on pourrait croire que <code>a</code> est une variable globale. Or, elle est déclarée à la fin. Comme l'interpréteur remonte la déclaration de la variable avant son affectation, cela revient à écrire :

<code>var a;
a = 1;
console.log(a);</code>

Prenez donc garde à bien déclarer vos variables au tout début de votre code, cela vous évitera bien des désagréments!

<h2>Les fonctions</h2>

<h3>Déclaration de fonction</h3>

Soit maintenant le code suivant :

<code>foo();
function foo() {
console.log('foo');
}</code>

Bien que la fonction <code>foo</code> soit déclarée après son appel, le résultat affiché est bien celui attendu, à savoir 'foo'. Dans ce cas, puisqu'il s'agit d'une déclaration, la fonction va être déplacée par l'interpréteur au-dessus de son appel. On aura donc :

<code>function foo() {
console.log('foo');
}
foo();</code>

<h3>Expression de fonction</h3>

Reprenons le précédent code et changeons-le pour en faire une expression de fonction en lieu et place d'une déclaration :

<code>foo();
var foo = function() {
console.log('foo');
}</code>

On obtient un joli "Uncaught TypeError : foo is not a function". En tant qu'expression de fonction, l'interpréteur JavaScript ne va ni plus ni moins que remonter la déclaration de la variable <code>foo</code> et laisser l'affectation à son emplacement initial. Cela revient donc à écrire le code suivant :

<code>var foo;
foo();
foo = function() {
console.log('foo');
}</code>

Ici encore, le résultat est finalement tout à fait logique. Il faut donc bien faire la distinction entre expression et déclaration de fonction, car l'interpréteur va les traiter de manière différente l'une de l'autre.

<h1>Cohabitation</h1>

Mettons un peu de folie dans ce code et ajoutons-y des variables :

<code>var f = 'foo';
foo();
function foo() {
console.log(f);
var f = 'bar';
}</code>

Étrange cet <code>undefined</code>? Pas tant que ça! Ici, nous avons deux variables du même nom, mais pas au même endroit. Si on applique le raisonnement, l'interpréteur organise le code comme suit :

<code>function foo() {
var f;
console.log(f);
f = 'bar';
}
var f;
f = 'foo';
foo();</code>

On voit bien que le contexte d'exécution des deux variables diffère. L'une est dans le contexte global, tandis que l'autre se trouve dans celui de la fonction. Elles sont remontées, mais pas au même niveau. Voilà pourquoi on obtient <code>undefined</code> à l'exécution de <code>foo</code>.

<h1>Un mot sur les variables globales...</h1>

Que se passe-t-il si on supprime la variable <code>f</code> du contexte global?

<code>foo();
foo();
function foo() {
try {
console.log(f);
}
catch (ex) {
console.log("f n'existe pas");
}
f = 'bar';
}</code>

L'interpréteur va organiser le code comme ceci :

<code>function foo() {
try {
console.log(f);
}
catch (ex) {
console.log("f n'existe pas");
}
f = 'bar'; // variable globale
}
foo();
foo();</code>

Comme <code>f</code> n'existe pas au premier appel de <code>foo</code>, cela va afficher 'f n'existe pas'. Or, on affecte juste après une valeur à <code>f</code> qui devient une variable globale. Car c'est la règle : <span style="text-decoration: underline;">toute variable non déclarée devient une variable globale</span>. On obtient donc :

<i>f n'existe pas
bar</i>

<h1>... et un petit sur le mode strict</h1>

Rajoutez donc la ligne <code>'use strict';</code> tout en haut du code ci-dessus. Vous obtenez une belle erreur vous indiquant que <code>f</code> n'est pas définie. Cela est dû au fait que vous êtes passé en mode strict, et que ce mode est, par définition, bien moins permissif que celui par défaut. Ainsi, en passant en mode strict, impossible de créer une variable globale. Ce mode vous prévient entre autre d'erreurs liées à des oublis de déclaration. Pour plus de détails, je vous laisse le soin d'aller regarder sur le site de <a title="Mozilla" href="https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Strict_mode" target="_blank">Mozilla</a>.

<h1>Conclusion</h1>

Le sujet est vaste, mais j'espère cependant que cet article vous aura permis de comprendre un peu mieux comment cela se passait quand on manipule des variables et des fonctions. C'est pour cela qu'on entend assez souvent dire qu'il faut déclarer toutes vos variables au début de votre programme. Utilisez quand c'est possible le mode strict afin de réduire les risques de bugs liés à une mauvaise utilisation des variables. Cela vous évitera des séances de débogage interminables sur des problèmes de références à undefined ou autre joyeusetés dont JavaScript est si friand!

Happy hoisting!
