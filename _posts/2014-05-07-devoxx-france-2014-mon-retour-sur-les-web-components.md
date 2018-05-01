---
layout: post
title: '[Devoxx France 2014] mon retour sur "Les Web Components"'
date: 2014-05-07 17:15
author: christophe michel
comments: true
categories: [Actu, devoxxFR, Evénements, web components]
---
<h1 style="text-align: justify;">Web components avec Google Polymer</h1>
<p style="text-align: justify;">Fin de première journée de Devoxx 2014, je vois une petite présentation sur Polymer, une biblothèque JS fournie par Google qui permet d'utiliser les Web Components dans ses applications Web. Vu que je suis en plein dans une mission de développement d'appli web bourrée de widgets qu'on voudrait réutilisables, je saute sur l'occasion.</p>
<p style="text-align: justify;">J'ai donc cette question en tête : est-ce que je vais enfin pouvoir m'affranchir de "composants" dont les éléments sont répartis dans 2 à 5-6 fichiers différents ?</p>
<p style="text-align: justify;">A <a href="https://twitter.com/jak78">Julian Jakubowski</a> (Octo) et <a href="https://twitter.com/romaintaz">Romain Linsolas</a> (Société Générale) d'apporter la réponse.</p>

<h2 style="text-align: justify;">Une histoire de super-héros réutilisables...</h2>
<p style="text-align: justify;">Nos deux speakers nous vendent de la création de "super héros" avec l'<a title="Marvel Developer Portal" href="http://developer.marvel.com/">API Marvel</a> : le but est d'avoir un composant qui affiche un super-héros issu de l'API Marvel, correctement packagé et réutilisable. Si vous voulez revoir le code qu’ils ont utilisé, il est <a href="https://github.com/linsolas/devoxx-polymer/">disponible sur Github</a>.</p>
<p style="text-align: justify;">Ils commencent avec une approche volontairement insatisfaisante de code copié/collé, puis commencent à définir un Web Component qui va factoriser le code existant.</p>
<p style="text-align: justify;">Un Web Component se compose de deux parties :</p>

<ul style="text-align: justify;">
	<li>Un template qui va définir le DOM du composant.</li>
	<li>Du code JS qui fournit son comportement.</li>
</ul>
<h2 style="text-align: justify;">Où on apprend que Polymer n'a rien à voir avec le cumul des mandats</h2>
<p style="text-align: justify;">Ce Web Component est écrit avec la syntaxe <a href="http://www.polymer-project.org/">Polymer</a> : un tag <code>&lt;polymer-element&gt;</code> qui contient un tag <code>&lt;template&gt;</code> pour le html et un tag <code>&lt;script&gt;</code> pour la logique.</p>

<pre style="background: #000; color: #f8f8f8;"><span style="color: #89bdff;">&lt;<span style="color: #89bdff;">polymer</span><span style="color: #89bdff;">-element</span> <span style="color: #89bdff;">name</span>=<span style="color: #65b042;">"sample-component"</span>&gt;</span>
    <span style="color: #89bdff;">&lt;<span style="color: #89bdff;">template</span>&gt;</span>
        <span style="color: #aeaeae; font-style: italic;">&lt;!-- Code HTML --&gt;</span>
    <span style="color: #89bdff;">&lt;/<span style="color: #89bdff;">template</span>&gt;</span>

    &lt;<span style="color: #e0c589;">script</span>&gt;
        <span style="color: #aeaeae; font-style: italic;">// Code JS</span>
    &lt;/<span style="color: #e0c589;">script</span>&gt;
<span style="color: #89bdff;">&lt;/<span style="color: #89bdff;">polymer</span><span style="color: #89bdff;">-element</span>&gt;</span></pre>
<p style="text-align: justify;">Polymer est une implémentation polyfill (via platform.js) de l'API Web Components HTML qui permet de compléter les lacunes des navigateurs dans le support de cette techno récente.</p>
<p style="text-align: justify;">Nos deux speakers font évoluer peu à peu leur composant pour en faire un widget qui affiche une image du super-héros, son nom et sa description.</p>
<p style="text-align: justify;">Ils nous montrent ensuite qu'en plus du DOM et du comportement écrit en Javascript, on peut également intégrer au composant une feuille de style, via une balise <code>&lt;style&gt;</code> qu'on insérera dans le template.</p>
<p style="text-align: justify;">En utilisant le sélecteur <code>:host</code>, on désigne le composant, tout en ayant par ailleurs accès à tous les sélecteurs et toutes les règles de style habituelles. On peut également styler explicitement un composant web dans la feuille de style de la page en utilisant un sélecteur qui porte sur le composant lui-même.</p>

<h2 style="text-align: justify;">Des arbres cachés dans l'ombre ?</h2>
<p style="text-align: justify;">Et là si vous avez bien tout suivi, vous devez vous dire qu'il y a une arnaque ou un truc. En effet, qu'est-ce qui m'empêche de faire un composant avec une feuille de style qui a des sélecteurs trop peu sélectifs et qui affectent des éléments en dehors du composant ?</p>
<p style="text-align: justify;">Et dans l'autre sens, est-ce que des sélecteurs de notre application web ne risquent pas de dénaturer un composant inclus dans une de ses pages à cause d'une malheureuse coïncidence de nom de classe ou d'id ?</p>
<p style="text-align: justify;">En somme, un Web Component ne semble pouvoir fonctionner que s'il est soigneusement isolé du reste de la page pour éviter des effets de bord.</p>
<p style="text-align: justify;"><img alt="" src="http://i.imgur.com/R4nBmuP.jpg" />
<span style="color: #c0c0c0;">Crédit Photo : <a href="https://www.flickr.com/photos/atillavibes/">Atilla Kefeli</a> (CC BY-ND 2.0) </span></p>
<p style="text-align: justify;">Les navigateurs ont une élégante solution à ce problème : le Shadow DOM. Il s'agit d'éléments qui sont rattachés au DOM principal, mais qui en sont isolés : ils seront rendus par le navigateur mais un parcours du DOM ne les traversera pas. Par ailleurs, les règles CSS sont cloisonnées entre le DOM principal et le Shadow DOM (en pratique, ce cloisonnement existe aussi entre toutes les arborescences qui existent dans le Shadow DOM pour éviter des interactions non désirées entre Web Components).</p>
<p style="text-align: justify;">En plus d'être masqués, les éléments du Shadow DOM sont cloisonnés au niveau CSS : les styles déclarés dans un Web Component ne déteignent pas sur le reste du document et inversement.</p>

<h2 style="text-align: justify;">Un template dans un verre d'eau</h2>
<p style="text-align: justify;"><span style="color: #c0c0c0;"><i>(Ce jeu de mots était complètement gratuit)</i></span></p>
<p style="text-align: justify;">Les templates Polymer sont peuplés via un objet de modèle qui permet de remplacer des variables, indiquées entre doubles accolades. Cet objet est celui qui est renvoyé à la fin du script de composant.</p>
<p style="text-align: justify;">Notez qu'il contient un attribut <code>ready</code> qui contient le callback utilisé par Polymer pour initialiser le composant quand son DOM sera chargé dans la page.</p>
<p style="text-align: justify;">Notez également que l'on ne peut pas appeler une méthode du modèle directement dans le template, on doit utiliser une property avec le mot-clé <code>get</code>.</p>
<p style="text-align: justify;">Voici à quoi ressemble le squelette complet d’un composant :</p>

<pre style="background: #000; color: #f8f8f8;"><span style="color: #89bdff;">&lt;<span style="color: #89bdff;">polymer</span><span style="color: #89bdff;">-element</span> <span style="color: #89bdff;">name</span>=<span style="color: #65b042;">"sample-component"</span> <span style="color: #89bdff;">attributes</span>=<span style="color: #65b042;">"sampleAttribute"</span>&gt;</span>
    <span style="color: #89bdff;">&lt;<span style="color: #89bdff;">template</span>&gt;</span>
        <span style="color: #89bdff;">&lt;<span style="color: #89bdff;">h1</span>&gt;</span>{{info}}<span style="color: #89bdff;">&lt;/<span style="color: #89bdff;">h1</span>&gt;</span>
    <span style="color: #89bdff;">&lt;/<span style="color: #89bdff;">template</span>&gt;</span>

    &lt;<span style="color: #e0c589;">script</span> <span style="color: #e0c589;">src</span>=<span style="color: #65b042;">"/scripts/script.js"</span>&gt;&lt;/<span style="color: #e0c589;">script</span>&gt;

    &lt;<span style="color: #e0c589;">script</span>&gt;
        <span style="color: #99cf50;">var</span> module <span style="color: #e28964;">=</span> (<span style="color: #99cf50;">function</span>() {
            <span style="color: #aeaeae; font-style: italic;">/* Méthodes privées */</span>

            <span style="color: #e28964;">return</span> {
                <span style="color: #89bdff;">ready</span>: <span style="color: #99cf50;">function</span> () {
                    <span style="color: #aeaeae; font-style: italic;">/* Callback Polymer */</span>
                },

                get info() {
                    <span style="color: #aeaeae; font-style: italic;">/* Attribut */</span>
                },

                <span style="color: #aeaeae; font-style: italic;">/* Méthodes publiques */</span>
            }
        })();

        Polymer(<span style="color: #65b042;">'sample-component'</span>, module);
    &lt;/<span style="color: #e0c589;">script</span>&gt;
<span style="color: #89bdff;">&lt;/<span style="color: #89bdff;">polymer</span><span style="color: #89bdff;">-element</span>&gt;</span></pre>
<p style="text-align: justify;">Julian et Romain concluent sur la possibilité d'externaliser un ou plusieurs Web Components dans un fichier externe via  <code>&lt;link rel="import" ... /&gt;</code>, ce qui soulèvera des questions sur les risques potentiels de XSS restées en suspens.</p>

<h2 style="text-align: justify;">Voyons plus loin<b> </b></h2>
<p style="text-align: justify;">Cette présentation m’a définitivement donné envie de creuser la question, mais il y a quelques points importants qui restent plutôt limitants pour une utilisation en production dans de nombreux projets :</p>

<ul style="text-align: justify;">
	<li>Dans certaines circonstances, l’utilisation d’imports HTML peut bloquer le rendu. On veillera à suivre <a title="HTML imports for Web Components" href="http://www.html5rocks.com/en/tutorials/webcomponents/imports/">les recommandations du site HTML5 Rocks</a> pour éviter les principaux écueils.</li>
	<li>Le support d’Internet Explorer est condamné à ne pas être performant : IE n’implémente ni le Shadow DOM, ni les templates HTML. Si Polymer fournit un polyfill pour IE10+ (fonctionne également avec IE9 même si le support de ce navigateur n’était pas prévu), les performances vont forcément s’en ressentir. Les données fournies par <a href="http://caniuse.com">Can I Use ? </a>n'inspirent pas l'optimisme :</li>
</ul>
<p style="text-align: justify;"><img alt="" src="http://i.imgur.com/CY27rhH.png" /></p>
<p style="text-align: justify;">Si toutefois ça ne vous décourage pas de tenter l’expérience, je termine avec quelques infos pour la route.</p>
<p style="text-align: justify;">Romain Linsolas vient de publier sur son propre blog <a title="Introductions aux Web Components avec Google Polymer" href="http://linsolas.github.io/blog/2014/05/05/introduction-aux-web-components-avec-google-polymer/">une introduction avec Web Components</a> avec un exemple différent et des explications plus détaillées. C’est un bon point de départ pour utiliser Polymer :</p>
<p style="text-align: justify;">Polymer est distribuée comme package <a title="Bower, a package manager for the web" href="http://bower.io/">Bower</a>, vous aurez donc besoin de <a title="Node.js" href="http://nodejs.org/">Node.js</a> et Bower. Comme le dit Romain dans son article, <a title="The Web' scaffolding tool for modern webapps" href="http://yeoman.io/">Yeoman</a> et <a title="Grunt, the JavaScript Task Runner" href="http://gruntjs.com/">Grunt</a> vous faciliteront la vie si vous travaillez en local (les imports HTML ont des restrictions de sécurité assez sévères en local, il est donc peu pratique de travailler avec eux sans que le site web soit servi par un serveur Web).</p>
<p style="text-align: justify;">Et si vous êtes du genre velu, vous pouvez également vous plonger dans les spécifications W3C du <a title="Shadow DOM W3C Working Draft" href="http://www.w3.org/TR/shadow-dom/">Shadow DOM</a> et des <a title="HTML Templates W3C Working Group Note " href="http://www.w3.org/TR/html-templates/">HTML Templates</a>. N'hésitez pas à me faire part de vos retours sur cette techno, tout particulièrement si vous faites partie des téméraires à l'avoir utilisée en prod.</p>
<p style="text-align: justify;">Les présentations comme celles-ci sont celles que je préfère à Devoxx : elles donnent envie d'en savoir plus tout en proposant suffisamment de contenu concret, même après avoir fait quelques expériences moi-même j'ai abandonné l'idée d'utiliser Polymer pour des Web Components custom chez mon client actuel. Mais ce n'est que partie remise...</p>
