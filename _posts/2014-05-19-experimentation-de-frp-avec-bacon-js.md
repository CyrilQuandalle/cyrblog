---
layout: post
title: Expérimentation de FRP avec Bacon.js
date: 2014-05-19 09:35
author: jerome prudent
comments: true
categories: [bacon, Fonctionnel, frp, immutable, javascript, Programmation, reactive]
---
<div class="body" style="text-align: justify;">
<h2>La théorie</h2>
<h3>Étymologie</h3>
Tout acronyme mérite une petite explication. C'est souvent par là qu'il faut commencer pour mieux comprendre une technologie.

FRP = Functional Reactive Programming

Reactive : d'après <a href="http://wordnetweb.princeton.edu/perl/webwn?s=reactive">wordnet</a> "qui réagit à un stimulus".

Functional Programming: fait référence à la <a href="http://fr.wikipedia.org/wiki/Programmation_fonctionnelle">programmation fonctionnelle</a>
<h3>Analogie</h3>
L'analogie que l'on retrouve le plus souvent sur le net pour expliquer FRP est la feuille de calcul d'un tableur.

Certaines cellules contiennent des <code>valeurs</code> que l'utilisateur peut changer, et d'autres des <code>formules</code>.

Par exemple

<a href="http://www.arolla.fr/blog/wp-content/uploads/2014/05/spreadsheet.png"><img class="alignnone size-full wp-image-2445" alt="spreadsheet" src="http://www.arolla.fr/blog/wp-content/uploads/2014/05/spreadsheet.png" width="338" height="81" /></a>

Appliqué à FRP une feuille de calcul s'explique de la manière suivante :
<ul>
    <li><code>A1</code> et <code>B1</code> sont des <em>streams d'events</em>. Quand un utilisateur change la valeur de la cellule, un <em>event</em> est envoyé à la cellule qui change alors sa <em>valeur courante</em></li>
    <li><code>C1</code> est aussi un <em>stream d'events</em>. Seulement, les événements ne sont plus générés directement par l'utilisateur mais induits par un changement de <em>valeur courante</em> de <code>A1</code> et <code>B1</code>.
<code>C1</code> <em>réagit</em>, <em>observe</em>, <em>écoute</em> les <em>événements</em> des deux autres cellules.</li>
</ul>
C'est une sorte d'implémentation du fameux <a href="http://fr.wikipedia.org/wiki/Observateur_%28patron_de_conception%29">observateur</a>.
<h3>Vocabulaire</h3>
Sur le net on trouvera un vocabulaire éparse et confus pour désigner des concepts sémantiquement identiques. Énumérons :
<ul>
    <li><em>event</em> : les stimulus de l'application</li>
    <li><em>event stream</em>, <em>property</em>, <em>behaviour</em>, <em>observable</em> : une variable qui réagit à des <em>events</em></li>
    <li><em>observer</em>, <em>listener</em> : généralement une fonction qui prend la <em>valeur courante</em> d'un <em>event stream</em> et réalise un <em>side effect</em>.</li>
    <li><em>side effect</em> : une action qui modifie l'environnement. Par exemple une sortie sur la console, une affectation de variable ou encore la manipulation du DOM.</li>
</ul>
<h2>Présentation Bacon.js</h2>
<a href="https://github.com/baconjs/bacon.js">Bacon.js</a> prend son inspiration en la librairie <a href="https://github.com/Reactive-Extensions/RxJS">RxJs</a> de Microsoft.

Bacon.js propose deux types d' <em>observable</em>:
<ul>
    <li><em>event stream</em></li>
    <li><em>property</em> : est très similaire à <em>event stream</em>, à l'exception qu'une <em>property</em> a toujours une valeur, dès sa création.</li>
</ul>
Bacon.js propose des méthodes pour passer d'un type d'<em>observable</em> à l'autre, et des méthodes pour créer et <a href="https://github.com/baconjs/bacon.js/wiki/Diagrams">mixer</a> des <em>observables</em>.
Le plus simple est d'aller faire un tour sur le <a href="https://github.com/baconjs/bacon.js/tree/master">readme</a> du projet pour vous faire une première idée. Visitez le <a href="https://github.com/baconjs/bacon.js/wiki">wiki</a> et les quelques exemples.
<h2>La pratique</h2>
<h3>Objectif</h3>
Dans ce document d'exploration on va implémenter un formulaire qui permet de choisir son pays. La liste des pays est récupérée d'un service qui cause en JSON. Il faudra gérer un spinner et la gestion des erreurs. Simple ? Cela dépend de la rigueur que l'on met à traiter tous les états ...

J'ai écrit un petit serveur tout simple en clojure dont les sources sont <a href="https://github.com/jprudent/jam-frp">ici</a>. Pour le builder, il faut récupérer <a href="http://leiningen.org/">lein</a> et
lancer la commande
<pre style="overflow-x: hidden; border: 0px solid #000;"><code style="overflow-x: hidden; border: 0px solid #000;">lein ring server
</code></pre>
Autrement, l'application complète (client et serveur) est déployée sur <a href="http://rocky-reef-4045.herokuapp.com/index.html">http://rocky-reef-4045.herokuapp.com/index.html</a>. Attention aux yeux, ça pique un peu comme design :)
<h3>Récupérer la liste des pays</h3>
Un GET sur <a href="http://rocky-reef-4045.herokuapp.com/countries">/countries</a> permet de récupérer la liste des pays. C'est un objet JSON de cette forme :
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="p">{</span>
      <span style="color: #ba2121;">"FRA"</span> <span class="o" style="color: #666666;">:</span> <span style="color: #ba2121;">"France"</span><span class="p">,</span>
      <span style="color: #ba2121;">"VEN"</span> <span class="o" style="color: #666666;">:</span> <span style="color: #ba2121;">"Venezuela"</span>
    <span class="p">}</span>
</code></pre>
</div>
Bacon.js s'intègre bien avec <a href="http://www.jquery.com">jQuery</a>. <code>fromPromise</code> permet de créer un <code>EventStream</code> pour un appel ajax renvoyant une promesse.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">ajaxCountries</span> <span class="o" style="color: #666666;">=</span> <span class="nx">Bacon</span><span class="p">.</span><span class="nx">fromPromise</span><span class="p">(</span><span class="nx">$</span><span class="p">.</span><span class="nx">ajax</span><span class="p">(</span><span class="nx">URL</span><span class="p">.</span><span class="nx">countries</span><span class="p">))</span> <span style="color: #408080; font-style: italic;">// creation d'un event stream à partir de la requête ajax</span>
      <span class="p">.</span><span class="nx">mapError</span><span class="p">(</span><span style="color: #ba2121;">"ERROR"</span><span class="p">);</span> <span style="color: #408080; font-style: italic;">// en cas d'erreur produit une string spéciale</span>
</code></pre>
</div>
Si l'appel réussit, le stream <code>ajaxContries</code> contiendra la réponse du serveur. Sinon l'état du stream est en erreur.
Le cas d'erreur est un peu spécial, il ne produit pas de valeur mais fait basculer l'état du stream à <code>error</code>. <code>mapError</code> permet de produire quand même une valeur en cas d'erreur.

A des fins des test, environ 1 fois sur 4 ça crache une erreur 500. D'expérience, la gestion des erreurs dans une application faisant un usage d'AJAX doit être prévue depuis la conception.

Votre application Javascript <strong>doit</strong> prévoir les cas d'erreur. Si elle ne les prévoit pas vous aurez sûrement des erreurs de déférencement, et un freeze de l'application. True story.

Donc nous allons déjà adresser le cas d'erreur en créant une nouvelle <code>Property</code>.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">isError</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">v</span><span class="p">){</span>
      <span class="k" style="color: #008000; font-weight: bold;">return</span> <span class="nx">v</span> <span class="o" style="color: #666666;">===</span> <span style="color: #ba2121;">"ERROR"</span><span class="p">;</span>
    <span class="p">};</span>

    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">isErrorAjaxCountries</span> <span class="o" style="color: #666666;">=</span> <span class="nx">ajaxCountries</span>
        <span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">isError</span><span class="p">)</span>
        <span class="p">.</span><span class="nx">toProperty</span><span class="p">(</span><span class="kc" style="color: #008000; font-weight: bold;">false</span><span class="p">)</span>
        <span class="p">.</span><span class="nx">skipDuplicates</span><span class="p">();</span>
</code></pre>
</div>
<code>map</code> permet de transformer une valeur en une autre valeur. Ici, si la valeur vaut <code>"ERROR"</code> on map à <code>true</code> sinon <code>false</code>.
<code>map</code> et <code>mapError</code> renvoient un nouvel <code>EventStream</code> et prennent normalement une fonction en paramètre. Cette fonction prend en
paramètre la <em>valeur</em> de l'évènement et retourne la nouvelle valeur.

Ici on passe une constante. C'est un sucre syntaxique pour une fonction qui serait similaire à :
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="nx">constant</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">v</span><span class="p">){</span>
      <span class="k" style="color: #008000; font-weight: bold;">return</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(){</span><span class="k" style="color: #008000; font-weight: bold;">return</span> <span class="nx">v</span><span class="p">};</span>
    <span class="p">}</span>

    <span class="nx">stream</span><span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">constant</span><span class="p">(</span><span style="color: #ba2121;">"ERROR"</span><span class="p">));</span> <span style="color: #408080; font-style: italic;">// le stream ne contiendra que des true</span>
</code></pre>
</div>
<code>toProperty</code> transforme un <code>EventStream</code> en une <code>Property</code> avec une <em>valeur par défaut</em>, ici <code>false</code>.
En effet, quel que soit l'état de l'application on doit savoir s'il faut ou non afficher le message d'erreur.
C'est la principale différence entre un <code>EventStream</code>, qui peut ne pas avoir de valeur (pendant le temps de la requête),
et une <code>Property</code> qui a toujours une valeur.

<code>skipDuplicate</code> est une petite optimisation qui empêche que deux valeurs consécutives dans le stream soient égales.

Pour résumé, voici les 2 états possibles des deux streams que nous avons créés :

<a href="http://www.arolla.fr/blog/wp-content/uploads/2014/05/ajaxCountries.png"><img class="alignnone size-full wp-image-2439" alt="ajaxCountries" src="http://www.arolla.fr/blog/wp-content/uploads/2014/05/ajaxCountries.png" width="492" height="374" /></a>

Pour le moment nous n'avons écrit que des <strong>définitions</strong>. Ce sont des vérités qui seront toujours vraies, quelque soit l'état de l'application.
Nous n'avons que des variables immutables. Aucun <em>side effect</em>.

Attaquons nous maintenant aux side effects.

Le DOM sera produit à partir du fichier HTML suivant :
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="html" style="overflow-x: hidden; border: 0px solid #000;">    <span class="cp" style="color: #bc7a00;">&lt;!DOCTYPE HTML&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;html&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;meta</span> <span class="na" style="color: #7d9029;">charset=</span><span class="s" style="color: #ba2121;">"UTF-8"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;</span>
        <span class="nt" style="color: #008000; font-weight: bold;">&lt;head&gt;</span>
            <span class="nt" style="color: #008000; font-weight: bold;">&lt;link</span> <span class="na" style="color: #7d9029;">rel=</span><span class="s" style="color: #ba2121;">"stylesheet"</span> <span class="na" style="color: #7d9029;">href=</span><span class="s" style="color: #ba2121;">"index.css"</span> <span class="na" style="color: #7d9029;">media=</span><span class="s" style="color: #ba2121;">"all"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;</span>
        <span class="nt" style="color: #008000; font-weight: bold;">&lt;/head&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;body&gt;</span>
        <span class="nt" style="color: #008000; font-weight: bold;">&lt;span</span> <span class="na" style="color: #7d9029;">class=</span><span class="s" style="color: #ba2121;">"error"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;&lt;/span&gt;</span>

        <span class="nt" style="color: #008000; font-weight: bold;">&lt;script </span><span class="na" style="color: #7d9029;">src=</span><span class="s" style="color: #ba2121;">"http://cdnjs.cloudflare.com/ajax/libs/bacon.js/0.7.10/bacon.js"</span> <span class="na" style="color: #7d9029;">type=</span><span class="s" style="color: #ba2121;">"text/javascript"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;&lt;/script&gt;</span>
        <span class="nt" style="color: #008000; font-weight: bold;">&lt;script </span><span class="na" style="color: #7d9029;">src=</span><span class="s" style="color: #ba2121;">"http://code.jquery.com/jquery-1.10.1.min.js"</span> <span class="na" style="color: #7d9029;">type=</span><span class="s" style="color: #ba2121;">"text/javascript"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;&lt;/script&gt;</span>
        <span class="nt" style="color: #008000; font-weight: bold;">&lt;script </span><span class="na" style="color: #7d9029;">src=</span><span class="s" style="color: #ba2121;">"index.js"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;&lt;/script&gt;</span>

    <span class="nt" style="color: #008000; font-weight: bold;">&lt;/body&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;/html&gt;</span>
</code></pre>
</div>
Un <code>span</code> contient l'erreur. Le reste du document est de la machinerie.

Maintenant, traitons visuellement le cas d'erreur:
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">showOrHideErrorMessage</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">show</span><span class="p">)</span> <span class="p">{</span>
      <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span style="color: #ba2121;">"showError = "</span><span class="p">,</span> <span class="nx">show</span><span class="p">);</span>
      <span class="k" style="color: #008000; font-weight: bold;">if</span><span class="p">(</span><span class="nx">show</span><span class="p">)</span> <span class="nx">$</span><span class="p">(</span><span style="color: #ba2121;">".error"</span><span class="p">).</span><span class="nx">show</span><span class="p">();</span>
      <span class="k" style="color: #008000; font-weight: bold;">else</span> <span class="nx">$</span><span class="p">(</span><span style="color: #ba2121;">".error"</span><span class="p">).</span><span class="nx">hide</span><span class="p">();</span>
    <span class="p">}</span>

    <span class="nx">isErrorAjaxCountries</span><span class="p">.</span><span class="nx">onValue</span><span class="p">(</span><span class="nx">showOrHideErrorMessage</span><span class="p">);</span>
</code></pre>
</div>
A chaque fois qu'une nouvelle valeur arrive dans la <code>Property</code> <code>isErrorAjaxCountries</code>, on appelle la fonction chargée d'exécuter le <em>side effect</em>, ici <code>showOrHideErrorMessage</code>.
Cette fonction peut faire n'importe quoi. Ici elle cache le span si la valeur est <code>false</code>, sinon elle l'affiche.

Nous avons géré un appel ajax en traitant le cas d'erreur avec 2 vars et 2 fonctions.
<h3>Ajout du spinner de chargement</h3>
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">isOngoingAjaxCountries</span> <span class="o" style="color: #666666;">=</span> <span class="nx">isErrorAjaxCountries</span><span class="p">.</span><span class="nx">awaiting</span><span class="p">(</span><span class="nx">ajaxCountries</span><span class="p">);</span>
</code></pre>
</div>
On <strong>définit</strong> une nouvelle <code>Property</code> <code>isOngoingAjaxCountries</code> qui vaut :
<ul>
    <li><code>false</code> tant que l'un des 2 <code>EventStream</code> n'a pas été alimenté ou dès que <code>isErrorAjaxCountries</code> a été alimenté par <code>ajaxCountries</code></li>
    <li><code>true</code> tant que <code>isErrorAjaxCountries</code> n'a pas été alimenté par <code>ajaxCountries</code></li>
</ul>
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="html" style="overflow-x: hidden; border: 0px solid #000;">  <span class="nt" style="color: #008000; font-weight: bold;">&lt;span</span> <span class="na" style="color: #7d9029;">class=</span><span class="s" style="color: #ba2121;">"spinner"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;&lt;/span&gt;</span>
</code></pre>
</div>
Visuellement, le spinner n'est qu'un <code>span</code> que l'on décide d'afficher ou non.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">showOrHide</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">show</span><span class="p">,</span> <span class="nx">selector</span><span class="p">){</span>
      <span class="k" style="color: #008000; font-weight: bold;">if</span><span class="p">(</span><span class="nx">show</span><span class="p">)</span> <span class="nx">selector</span><span class="p">.</span><span class="nx">show</span><span class="p">();</span>
      <span class="k" style="color: #008000; font-weight: bold;">else</span> <span class="nx">selector</span><span class="p">.</span><span class="nx">hide</span><span class="p">();</span>
    <span class="p">};</span>

    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">showOrHideSpinner</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">show</span><span class="p">)</span> <span class="p">{</span>
      <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span style="color: #ba2121;">"showSpinner = "</span><span class="p">,</span> <span class="nx">show</span><span class="p">)</span>
      <span class="nx">showOrHide</span><span class="p">(</span><span class="nx">show</span><span class="p">,</span> <span class="nx">$</span><span class="p">(</span><span style="color: #ba2121;">".spinner"</span><span class="p">));</span>
    <span class="p">};</span>

    <span class="nx">isOngoingAjaxCountries</span><span class="p">.</span><span class="nx">onValue</span><span class="p">(</span><span class="nx">showOrHideSpinner</span><span class="p">);</span>
</code></pre>
</div>
On applique le <em>side effect</em> pour afficher ou non le spinner, de façon similaire au message d'erreur.
<h3>Affichage d'une boîte de saisie du pays</h3>
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">isDoneAjaxCountries</span> <span class="o" style="color: #666666;">=</span> <span class="nx">ajaxCountries</span>
        <span class="p">.</span><span class="nx">map</span><span class="p">(</span><span class="nx">not</span><span class="p">(</span><span class="nx">isError</span><span class="p">))</span>
        <span class="p">.</span><span class="nx">toProperty</span><span class="p">(</span><span class="kc" style="color: #008000; font-weight: bold;">false</span><span class="p">)</span>
        <span class="p">.</span><span class="nx">skipDuplicates</span><span class="p">();</span>
</code></pre>
</div>
On définit un <code>EventStream</code> qui est un peu l'inverse de <code>isErrorAjaxCountries</code>.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="html" style="overflow-x: hidden; border: 0px solid #000;">    <span class="nt" style="color: #008000; font-weight: bold;">&lt;input</span> <span class="na" style="color: #7d9029;">type=</span><span class="s" style="color: #ba2121;">"text"</span> <span class="na" style="color: #7d9029;">name=</span><span class="s" style="color: #ba2121;">"country"</span> <span class="na" style="color: #7d9029;">list=</span><span class="s" style="color: #ba2121;">"countries"</span><span class="nt" style="color: #008000; font-weight: bold;">/&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;datalist</span> <span class="na" style="color: #7d9029;">id=</span><span class="s" style="color: #ba2121;">"countries"</span><span class="nt" style="color: #008000; font-weight: bold;">&gt;</span>
    <span class="nt" style="color: #008000; font-weight: bold;">&lt;/datalist&gt;</span>
</code></pre>
</div>
Visuellement, c'est une combobox éditable.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">showOrHideInputCountry</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">show</span><span class="p">){</span>
      <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span style="color: #ba2121;">"show country input"</span><span class="p">,</span> <span class="nx">show</span><span class="p">);</span>
      <span class="nx">showOrHide</span><span class="p">(</span><span class="nx">show</span><span class="p">,</span> <span class="nx">$</span><span class="p">(</span><span style="color: #ba2121;">"[name='country']"</span><span class="p">));</span>
    <span class="p">}</span>

    <span class="nx">isDoneAjaxCountries</span><span class="p">.</span><span class="nx">onValue</span><span class="p">(</span><span class="nx">showOrHideInputCountry</span><span class="p">);</span>
</code></pre>
</div>
On applique le <em>side effect</em> pour afficher ou non l'input, de façon similaire au message d'erreur.
<h3>Remplissage de la boîte de saisie</h3>
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">countriesList</span> <span class="o" style="color: #666666;">=</span> <span class="nx">ajaxCountries</span>
      <span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">not</span><span class="p">(</span><span class="nx">isError</span><span class="p">));</span>
</code></pre>
</div>
On définit un <code>EventStream</code> qui nous renseigne sur la disponibilité
de la liste des pays.
<div class="highlight">
<pre style="overflow-x: hidden; border: 0px solid #000;"><code class="javascript" style="overflow-x: hidden; border: 0px solid #000;">    <span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">fillCountries</span> <span class="o" style="color: #666666;">=</span> <span class="kd" style="color: #008000; font-weight: bold;">function</span><span class="p">(</span><span class="nx">countries</span><span class="p">){</span>
      <span class="k" style="color: #008000; font-weight: bold;">for</span><span class="p">(</span><span class="kd" style="color: #008000; font-weight: bold;">var</span> <span class="nx">countryCode</span> <span class="k" style="color: #008000; font-weight: bold;">in</span> <span class="nx">countries</span><span class="p">){</span>
        <span class="nx">$</span><span class="p">(</span><span style="color: #ba2121;">"#countries"</span><span class="p">).</span><span class="nx">append</span><span class="p">(</span><span style="color: #ba2121;">'&lt;option value="'</span><span class="o" style="color: #666666;">+</span> <span class="nx">countries</span><span class="p">[</span><span class="nx">countryCode</span><span class="p">]</span> <span class="o" style="color: #666666;">+</span><span style="color: #ba2121;">'"&gt;'</span> <span class="o" style="color: #666666;">+</span> <span class="nx">countryCode</span> <span class="o" style="color: #666666;">+</span> <span style="color: #ba2121;">'&lt;/option&gt;'</span><span class="p">);</span>
      <span class="p">}</span>
    <span class="p">};</span>

    <span class="nx">countriesList</span><span class="p">.</span><span class="nx">onValue</span><span class="p">(</span><span class="nx">fillCountries</span><span class="p">);</span>
</code></pre>
</div>
On remplit la datalist avec la liste des pays.
<h2>Récapitulons</h2>
Le code javascript complet est accessible <a href="https://github.com/jprudent/jam-frp/blob/master/src/resources/public/index.js">ici</a>, je vous conseille de le parcourir rapidement. Le code est divisé en deux parties.

D'une part, une partie que j'appelle <em>définitions</em>. Cette partie est agnostique à la technologie. Elle décrit le flux de données de notre application.

Chaque <code>Observable</code> défini dans cette partie a une sémantique propre et vraie quel que soit l'état de l'application. L'ensemble forme un graphe de dépendances.
<ul>
    <li><code>ajaxCountries</code> : le résultat de la requête ajax. C'est l'observable tout en haut de la pyramide des dépendances.</li>
    <li><code>isErrorAjaxCountries</code> : contient <code>true</code> si la requête s'est mal passée.</li>
    <li><code>isDoneAjaxCountries</code> : contient <code>true</code> si la requête s'est bien passée.</li>
    <li><code>isOngoingAjaxCountries</code> : contient <code>true</code> si la requête ajax est en cours.</li>
    <li><code>countriesList</code> : contient la liste des pays.</li>
</ul>
<a href="http://www.arolla.fr/blog/wp-content/uploads/2014/05/dependencies.png"><img class="alignnone size-full wp-image-2440" alt="dependencies" src="http://www.arolla.fr/blog/wp-content/uploads/2014/05/dependencies.png" width="639" height="317" /></a>

Je trouve tout cela clair : on a la requête, ses 3 états et sa valeur finale.
Je trouve aussi que c'est extensible. Par exemple on pourrait retravailler facilement les valeurs de <code>countriesList</code> avec une méthode <code>map</code> pour les passer en majuscule. Ou alors on aurait pu conditionner <code>ajaxCountries</code> par un autre <code>EventStream</code> pour ne faire la requête que sous certaines conditions ...

D'autre part, une partie appelée <em>side effects</em> qui ne contient que de la logique d'affichage :
<ul>
    <li><code>isErrorAjaxCountries.onValue</code> : on affiche ou pas un message d'erreur</li>
    <li><code>isOngoingAjaxCountries.onValue</code> : on affiche ou pas un spinner</li>
    <li><code>isDoneAjaxCountries.onValue</code> : on affiche ou pas la boîte de saisie à l'utilisateur</li>
    <li><code>countriesList.onValue</code> : on traite le résultat de la requête</li>
</ul>
J'ai un bon sentiment sur le code que j'ai écrit. Je n'ai pas l'impression d'avoir laissé trainer des bugs. On a presque l'impression d'avoir écrit une preuve mathématique.
On ne flippe pas sur un problème de déférencement non plus car toutes les variables sont immutables.
<h2>Difficultés</h2>
<h3>EventStream ou Property ?</h3>
Bacon.js fait une distinction entre <code>EventStream</code> et <code>Property</code>. Je trouve que ce n'est pas très pratique, et on est souvent en train de se demander quel est le type de notre Observable. Aussi, je trouve la barrière entre les deux concepts un peu floue. La seule différence semble être que la <code>Property</code> <em>peut</em> avoir une valeur par défaut.
<h3>Un graphe de dépendance</h3>
Le debuggage est assez compliqué, il n'est parfois pas évident de trouver pourquoi tel événement a eu lieu.
Avec un exemple assez conséquent, on oublie qui dépend de qui. Il faut reparcourir les définitions. Pouvoir extraire un arbre des dépendances et le visualiser synthétiserait et aiderait beaucoup le développeur. J'ai trop souvent recours à des <code>console.log()</code> pour debugger.
<h2>Verbosité ?</h2>
D'aucun me rétorquera qu'avec jQuery, on fait tout ça de manière moins verbeuse (<code>complete</code>, <code>error</code>, <code>success</code>, ...). J'ai pris une requête ajax en exemple, car cela reste conçis mais j'aurais pu trouver un exemple où jQuery n'offre pas autant de raccourcis.

J'ai également volontairement variabilisé les différentes étapes et les fonctions au lieu de les inliner. Je trouve ça plus clair, la syntaxe de Javascript étant très lourde.
<h2>Aller plus loin ?</h2>
J'ai à peine effleuré les capacité de Bacon. Il y a des tas de façons de travailler les streams (<code>merge</code>, <code>flatMap</code>, ...). Notons aussi qu'il existe un petit <code>event bus</code> qui peut se révéler pratique sur des pages de plus grande envergure.

Mais imaginons un scénario plus complexe où après avoir choisi son pays on l'invite à saisir son code postal. Dès que les 2 premiers chiffres sont saisis, on l'invite à choisir sa ville dans une liste récupérée en ajax, tout en lui laissant le choix de compléter son code postal en mettant à jour la liste des villes au fur et à mesure. On ne rend le bouton envoyer cliquable que si tout est saisi sans erreur. Le tout avec spinners et message d'erreur...
Ce que je viens de décrire, je l'ai vraiment implémenté une fois avec un jQuery 1.4 (pas de <code>complete</code>, <code>error</code>, <code>success</code>) et sans plugins (je ne vous dirai pas où, j'ai trop honte). Et c'était l'enfer. Il faudrait que j'essaye de réimplémenter ça ...
<h2>Ressources</h2>
<a href="https://github.com/baconjs/bacon.js">Bacon.js</a>

<a href="http://elm-lang.org/learn/What-is-FRP.elm">What is Functional Reactive Programming?</a>

<a href="http://www.ustream.tv/recorded/29299079">Vidéo du créateur de Bacon.js</a>

</div>

<p style="text-align: justify;"></p>
