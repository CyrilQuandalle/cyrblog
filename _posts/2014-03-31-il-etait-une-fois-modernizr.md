---
layout: post
title: Il était une fois... Modernizr
date: 2014-03-31 10:58
author: anne b
comments: true
categories: [Bonnes pratiques de dév, modernizr, Outils, web]
---
<p style="text-align: justify;"></p>

<div style="font-size: 13px; text-align: justify;"><em>Un jour la princesse interpella le grand chambellan :
"Grand chambellan venez par ici, j'ai à vous exposer un de mes soucis. Voyez-vous pour mon site j'ai une envie bien définie. J'aimerais que la homepage soit personnalisée en fonction de la dernière recherche effectuée ; mais voilà je suis désappointée par la retrocompatibilité... Auriez-vous une idée pour me sauver ?
- Évidemment ma princesse, j'ai la réponse à vos soucis : le browser storage va combler vos envies.
- Grand chambellan vous êtes merveilleux, mais que faites-vous des navigateurs qui sont vieux ?
- Là aussi point de panique, Modernizr et Yepnope sont pour cela des outils très pratiques.
- Grand chambellan vous êtes épatant, dites m'en plus je suis impatiente."
Et le grand chambellan alors expliqua :</em></div>

<h4 style="text-align: justify;"><span style="color: #993366;">1. JavaScript-driven feature detection</span></h4>

<p style="text-align: justify;">Modernizr permet de faire abstraction du user-agent en se basant sur la détection des fonctionnalités propres au HTML5 et CSS3. Fini le user-agent sniffing. Grâce à cela, il est possible d'écrire du javascript et du CSS de manière conditionnelle afin de gérer chaque situation, sans vous préoccuper du navigateur.</p>

<p style="text-align: justify;">Nous gérons les cas particuliers d'IE&lt;11, car dans la majorité des cas c'est là que le bât blesse ; mais au final, ce qui importe réellement c'est la fonctionnalité et pas tant le navigateur.
C'est ce que permet Modernizr : se concentrer sur l'essentiel : est-ce que le navigateur est capable de rendre la fonctionnalité à l'utilisateur, ou pas ; et s'il ne peut pas rendre la fonctionnalité, que pouvons nous faire en contrepartie.</p>

<h4 style="text-align: justify;"><span style="color: #993366;">2. How it works</span></h4>

<p style="text-align: justify;">Modernizr en lui-même permet uniquement de faire de la détection de fonctionnalité. Il est possible de le télécharger en version standalone ou accompagné de la librairie <a title="Yepnope" href="http://yepnopejs.com/" target="_blank">yepnope.js</a>.</p>

<p style="text-align: justify;">Ajouté à la détection de fonctionnalité de Modernizr, yepnope permet de télécharger de manière conditionnelle et asynchrone les scripts dont vous avez besoin et seulement si vous en avez besoin.</p>

<p style="text-align: justify;">Voici quelques exemples montrant l'évolution d'un code javascript avec l'intégration de Modernizr puis Yepnope.js.</p>

<p style="text-align: justify;"><span style="border-bottom: 1px dotted;">
1. Détection de la présence du window.localStorage en vanilla js :
</span></p>

<pre>function supportsLocalStorage() {
  try {
    return 'localStorage' in window &amp;&amp; window['localStorage'] !== null;
  } catch(e){
    return false;
  }
}</pre>

<p style="text-align: justify;"><span style="border-bottom: 1px dotted;">
2. Détection de la présence du window.localStorage avec Modernizr :
</span></p>

<pre>if (Modernizr.localstorage) {
  // window.localStorage est disponible !
} else {
  // pas de support natif du local storage
  // mettre ici le fallback ou une autre solution
}</pre>

<p style="text-align: justify;"><span style="border-bottom: 1px dotted;">
3. Détection de la présence du window.localStorage avec Modernizr et Yepnope.js :
</span></p>

<pre>Modernizr.load([
  {
    test : Modernizr.localStorage ,                       // &lt;-- booléen
    nope : 'localStorage-polyfill.js'*,                   // &lt;-- si le test est FAUX, on télécharge ce fichier
  },
  {
    test : Modernizr.websockets &amp;&amp; window.JSON,           // &lt;-- booléen
    yep : 'youpi.js'*,                                    // &lt;-- si le test est VRAI, on télécharge ce fichier
    nope : 'functional-polyfills.js'*,                    // &lt;-- si le test est FAUX, on télécharge ce fichier
    both : ['app.js', 'extra.js']*,                       // &lt;-- dans tous les cas, on télécharge ces 2 fichiers
    load : ['after.js', 'super.js']*,                     // &lt;-- dans tous les cas, on télécharge ces 2 fichiers
    complete : function () {
      // Fonction exécutée une fois que tous les fichiers de ce groupe ont été téléchargés
      // et exécutés, ainsi que les fichiers et fonctions du groupe précédent.
      myApp.init();
    }
  }
]);

* il faut indiquer l'URL du (des) fichier(s) à télécharger.</pre>

<p style="text-align: justify;">Il faut noter que Yepnope.js :</p>

<ul style="text-align: justify;">
    <li>télécharge les fichiers de manière asynchrone et parallèle,</li>
    <li>mais exécute les choses dans l'ordre où elles sont écrites,</li>
    <li>gère le téléchargement de fichiers CSS et JS. Tous les fichiers indiqués sont considérés comme des fichiers JS à moins que l'extension '.css' ne soit précisée.</li>
</ul>

<h4 style="text-align: justify;"><span style="color: #993366;">3. Highly customizable</span></h4>

<p style="text-align: justify;"><a title="Modernizr download" href="http://modernizr.com/download/" target="_blank">Sélectionnez et téléchargez</a> uniquement ce dont vous avez besoin, minimisé ou non.
Dans le cas d'ajout de détections supplémentaires, le script téléchargé contient l'url avec les options précédemment pré cochées :</p>

<pre>/* Modernizr 2.7.1 (Custom Build) | MIT &amp; BSD
* Build: http://modernizr.com/download/#-canvas-sessionstorage-cors
*/

>&gt; ici la détection des fonctionnalités HTML5 canvas et sessionStorage, ainsi que CORS ont été sélectionnées.</pre>

<h4 style="text-align: justify;"><span style="color: #993366;">4. Beware of... IE compatibility mode</span></h4>

<p style="text-align: justify;">Attention au mode de compatibilité d'Internet Explorer.</p>

<p style="text-align: justify;">Par exemple, vous êtes sous IE10 mais avec le mode de compatibilité IE7. Vous testez avec Modernizr si le JSON est nativement géré, voici le résultat :</p>

<pre>Modernizr.load({
  test: Modernizr.json, 
  nope: 'polyfill-json.js',
  complete: function () {
    // Something to do with JSON.    
  }
});</pre>

<p style="text-align: justify;">=&gt; 1. IE10 gère nativement le JSON, le test Modernizr.json renvoie TRUE,
=&gt; 2. donc le polyfill polyfill-json.js' n'est pas téléchargé,
=&gt; 3. mais en mode de compatibilité IE7, le JSON est désactivé au niveau du navigateur,
=&gt; 4. votre fonctionnalité ne fonctionnera pas et vous aurez des erreurs JS...</p>

<h4 style="text-align: justify;"><span style="color: #993366;">5. Don't forget the user</span></h4>

<p style="text-align: justify;">Faites bien attention à l'expérience utilisateur ; et surtout rappelez-vous qu'il n'y a aucun problème à ce qu'un site apparaisse et réagisse différemment en fonction du navigateur. C'est même tout à fait normal.</p>

<p style="text-align: justify;">Modernizr n'a pas pour but de "shooter" les anciens navigateurs à coup de polyfills afin d'en faire des simulacres de navigateurs dernier cri. Ce n'est pas parce que vous pouvez télécharger un polyfill que vous le devez. Modernizr n'est là que pour "moderniser" vos pratiques de développement, en vous permettant de vous concentrer sur le point central : la fonctionnalité, et par là l'expérience utilisateur.</p>

<p style="text-align: justify;">Reste à vous de décider de l'importance ou de la criticité de votre fonctionnalité : polyfill ou non ? amélioration progressive ou pas ?</p>

<h4 style="text-align: justify;"><span style="color: #993366;">6. Links</span></h4>

<ul>
    <li style="text-align: justify;"><a title="Modernizr" href="http://modernizr.com/" target="_blank">Modernizr</a></li>
    <li style="text-align: justify;"><a title="Yepnopejs" href="http://yepnopejs.com/" target="_blank">Yepnopejs</a> </li>
    <li style="text-align: justify;"><a title="Polyfills" href="https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-browser-Polyfills" target="_blank">Polyfills</a></li>
    <li style="text-align: justify;"><a title="Undetectables" href="https://github.com/Modernizr/Modernizr/wiki/Undetectables" target="_blank">Undetectable features</a></li>
    <li style="text-align: justify;"><a title="Dive into HTML5" href="http://diveintohtml5.info/" target="_blank">Dive into HTML5</a></li>
</ul>
