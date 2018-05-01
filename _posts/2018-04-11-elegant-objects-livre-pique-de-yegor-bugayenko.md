---
layout: post
title: Elegant Objects, le livre qui pique de Yegor Bugayenko
date: 2018-04-11 14:36
author: hadrien mens-pellen
comments: true
categories: [avis, Bonnes pratiques de dév, code, elegant, Je pense donc je blogue, Non classé, object, objects, yegor]
---
<div class="preview__inner-2">
<div class="cl-preview-section">

<img class="alignright" src="https://pbs.twimg.com/media/DLsIkDjWsAApVuS.jpg:small" alt="Le livre Elegant Objects" />Il y a quelques temps, je suis tombé sur le blog de <a href="http://www.yegor256.com/">Yegor Bugayenko</a>. Son sujet d’écriture principal est la programmation orientée objet. Il y expose ses visions sur des problèmes classiques et ses solutions objets. En suivant une série de valeurs et de principes objets, il a développé un style de code particulier.

</div>
<div class="cl-preview-section">

J’ai bien accroché à son blog. Il a des avis très tranchés. Parfois je suis d’accord, mais parfois, pas du tout !

</div>
<div class="cl-preview-section">

Pour présenter sa vision de la programmation objet, il a sorti un livre : Elegant Objects (en deux volumes). Arolla a bien voulu le commander pour sa bibliothèque ! \o/

</div>
<div class="cl-preview-section">

Mieux qu’un commentaire <a href="https://www.amazon.fr/" rel="noopener" target="_blank">Amazon</a> je me suis dit que vous le présenter vous permettrait de vous faire une idée sur l’intéret (ou pas) de le lire.

</div>
<div class="cl-preview-section">
<h1 id="résumé--spoiler">Résumé / Spoiler</h1>
</div>
<div class="cl-preview-section">
<h2 id="pour-les-devs-occupé.e.s">Pour les devs occupé.e.s</h2>
</div>
<div class="cl-preview-section">

Si vous voulez un <strong>avant goût</strong> du livre, vous pouvez lire des articles <strong><a href="http://www.yegor256.com/" rel="noopener" target="_blank">son blog</a></strong>. Le livre est, en fait, une compilation de plusieurs articles un peu plus développés.

</div>
<div class="cl-preview-section">

<strong>Le ton est polémique</strong> et les avis tranchés (c’est son style). Il y a de bons conseils, de moins bons (voire carrément mauvais xD). <strong>Son approche de l’objet est cohérente</strong> et justifiée.

</div>
<div class="cl-preview-section">

Avec ses 223 pages, et un contenu aéré, le livre se lit très vite (j’ai mis une journée et je ne suis pas un gros lecteur). C’est une lecture intéressante, mais je ne le conseillerais pas à un débutant.

</div>
<div class="cl-preview-section">
<h1 id="un-peu-plus-sur-le-bonhomme">Un peu plus sur le bonhomme</h1>
</div>
<div class="cl-preview-section">
<ul>
    <li>On le trouve partout sous le pseudo <a href="http://www.yegor256.com/" rel="noopener" target="_blank">Yegor256</a> (comme son blog)</li>
    <li>Son contenu est souvent référencé dans les aggrégateurs de blog comme DZone</li>
    <li>C’est le contributeur principal de la famille de librairies jcabi (que vous avez peut-être déja rencontré)</li>
    <li>Il décerne des prix de qualité de code, un des gagnants est junit-quickcheck, une librairie de property based testing en java.</li>
</ul>
</div>
<div class="cl-preview-section">
<h1 id="contenu">Contenu</h1>
</div>
<div class="cl-preview-section">

Les chapitres sont organisés autour de la “vie” des objets. En effet, son fil rouge est la personnification de ceux-ci. Il y aborde entre autres les sujets suivants :

</div>
<div class="cl-preview-section">
<ul>
    <li>Anti patterns de nommage</li>
    <li>Meilleures pratiques autour des constructeurs</li>
    <li>Meilleures pratiques de tailles d’objets et d’encapsulations</li>
    <li>L’immutabilité</li>
    <li>Les tests comme documentation et l’approche non mockiste</li>
    <li>Anti patterns OOP (méthodes statiques etc.)</li>
    <li>Meilleures pratiques de design d’API</li>
</ul>
</div>
<div class="cl-preview-section">
<h1 id="ce-que-jai-aimé">Ce que j’ai aimé</h1>
</div>
<div class="cl-preview-section">
<ul>
    <li>Contrairement aux exemples dans Clean Code qui peuvent être assez austères et long, ici ils sont concis et facile à comprendre</li>
    <li>Son approche va se rapprocher beaucoup du fonctionnel
<ul>
    <li>On compose des objets qui n’ont souvent qu’une seule fonction et qui composent d’autres objets. En appelant l’objet parent on déclenche l’évaluation de toute la grappe (pas à la construction donc).</li>
    <li>Tout immutable</li>
</ul>
</li>
</ul>
</div>
<div class="cl-preview-section">
<ul>
    <li>La cohérence du propos.</li>
    <li>Les grands principes qui forgent sa vision :
<ul>
    <li>Maintenabilité</li>
    <li>Lisibilité
<ul>
    <li>Code déclaratif -&gt; pour atteindre des bénéfices comme le partage avec le métier</li>
</ul>
</li>
    <li>Simplicité</li>
    <li>SRP : Single Responsibility Principle -&gt; Chaque objet devrait faire une seule chose</li>
    <li>Pur objet : Pas de statique, pas singletons, pas de services</li>
</ul>
</li>
</ul>
</div>
<div class="cl-preview-section">
<h2 id="conseils-que-je-retiens">Conseils que je retiens</h2>
</div>
<div class="cl-preview-section">
<ul>
    <li><strong>Penser à l’utilisateur de l’API</strong>, il faut lui faciliter la vie
<ul>
    <li>Nommage qui doit lui parler à lui</li>
    <li>Paramètres ouverts pour accepter le plus possible d’entrées</li>
    <li>Sortie très précise pour lui permettre de faire beaucoup de choses</li>
    <li>Le moins de méthodes possible</li>
</ul>
</li>
    <li><strong>Nommer les objets pour ce qu’ils sont</strong> (les données qu’ils encapsulent) pas ce qu’ils font
<ul>
    <li>Exemple :
<ul>
    <li>Mauvais nom : PrimeNumberGenerator</li>
    <li>Bon nom : PrimeNumbers</li>
</ul>
</li>
</ul>
</li>
    <li>Un objet ne doit pas encapsuler trop de choses (il dit 4 maxi) mais au moins encapsuler quelque chose sinon c’est du procédural.</li>
    <li>Il préfère séparer les méthodes qui font des choses de celles qui renvoient des choses (dérivé du SRP)
<ul>
    <li>Il conseille de nommer les méthodes qui renvoient des choses du nom de la valeur qu’ils renvoient (par exemple person.firstName() au lieu de person.getFirstName() )</li>
    <li>Et les méthodes qui font des choses avec des verbes</li>
</ul>
</li>
    <li>Eviter les services (qui ne sont que des collections de procédure (ni objet ni fonctionnel)).</li>
    <li>Eviter les noms en “er” (Mapper, Controller) ce sont des indicateurs qu’on repasse au procédural.</li>
</ul>
</div>
<div class="cl-preview-section">
<h1 id="ce-que-jai-moins-aimé">Ce que j’ai moins aimé</h1>
</div>
<div class="cl-preview-section">
<ul>
    <li>Les justifications de certaines de ses préférences sont parfois simplement basées sur la personnification de l’objet. On trouve parfois des citations comme “ce n’est pas courtois de demander ça à quelqu’un alors ne le demandez pas à un objet”.</li>
    <li><strong>Sa définition de l’immutabilité</strong>. Pour lui un objet qui contient une liste mutable est immutable si on ne peut pas remplacer l’instance de la liste par exemple. Son argumentation sur les intérêts de l’immutabilité est bonne (simplicité pour le debug, thread safety…). Mais pour moi la définition ne correspond pas.</li>
    <li><strong>Sa vision manichéenne</strong> lui est notamment reprochée par Sandro Mancuso. Personnellement je pense que c’est juste une simplification deson discours. De cette manière c’est plus simple à comprendre que s’il listait toute une suite d’exceptions à la règle. De plus, nous sommes déjà suffisamment prompts au compromis pour ne pas en rajouter.</li>
    <li><strong>La difficulté de tester le code</strong>. En expérimentant avec sa vision, j’arrive vite à un code difficilement testable. Comme le monsieur n’est pas un grand adepte du TDD, il ne donne que peu de conseils dessus. Comme toutes les classes sont censées être petites ce devrait être plus facilement testable. Il doit me rester du chemin pour m’adapter</li>
    <li>Il préconise de n’utiliser que des checked exceptions 0_0’</li>
    <li>Sa <strong>passion des décorateurs</strong>. Il voudrait même les utiliser pour remplacer les streams (aïe) :
<pre><code>  names = new Sorted (
      new Unique (
          new Capitalized (
              new Replaced (
                  new FileNames (
                      new Directory (
                          "/var/users/*.xml"
                      )
                  ),
                  "([^.]+)\\.xml",
                  "$1"
              )
          )
      )
  );
</code></pre>
</li>
</ul>
</div>
<div class="cl-preview-section">
<h1 id="en-conclusion">En conclusion</h1>
</div>
<div class="cl-preview-section">

Je ne pense pas que j’aimerais travailler sur une de ses bases de code. En particulier à cause des décorateurs, l’exemple de code ci-dessus m’est très difficilement compréhensible. Aussi, les concepts qu’il tire pour créer ses objets sont parfois un peu tirés par les cheveux (dans sa librairie de mail, il introduit des concepts d’enveloppes et de timbres qui, sans explications, sont difficiles à saisir).

</div>
<div class="cl-preview-section">

Pour l’instant, c’est le seul exemple que j’ai d’un code pur objet. Il m’a fait reconsidérer ma compréhension de ce paradigme. J’y réfléchis à deux fois avant de faire un service maintenant.

</div>
<div class="cl-preview-section">

Avec son approche, je vois des intérêts en terme de rangement du code et de compatibilité avec nos langages majoritaires. Cependant, certaines parties de son code ne me donnent vraiment pas envie. Donc je vais continuer à coder avec un mélange de procédural, d’objet et fonctionnel suivant ce qui me semble le plus pratique logique et maintenable. Le livre m’aura au moins permis d’avoir l’esprit clair sur le paradigme que je choisis.

</div>
<div class="cl-preview-section">

Et pour vous, à quoi ressemble un code pur objet ?

</div>
</div>
