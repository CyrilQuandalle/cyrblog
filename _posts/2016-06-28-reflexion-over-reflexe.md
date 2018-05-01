---
layout: post
title: Réflexion over réflexe
date: 2016-06-28 12:00
author: raphael squelbut
comments: true
categories: [Bonnes pratiques de dév]
---
<h1>Agissez par réflexion, non par réflexe</h1>

Le texte qui suit est un élan du coeur ayant pour modeste but d'amener le lecteur à prendre du recul sur sa façon de travailler et surtout analyser ses réactions face aux problèmes rencontrés. Il n'a pas vocation à présenter des théories générales, ni défendre des best practices. Il ne s'agit pas non plus de remettre en cause votre organisation ou les processus qui guident votre travail. L'ambition est moindre.
Dans le meilleur des cas, j'aimerais qu'à la suite de cette lecture, vous vous arrêtiez un instant pour mettre en doute vos solutions, vos méthodes et leur utilisation à l'instant T.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/7/74/Penseur_-_Hotel_Biron.JPG/256px-Penseur_-_Hotel_Biron.JPG" alt="alt text" title="Le Penseur" />

A travers quelques exemples réels, j'espère montrer que pour un même problème, il existe de nombreuses bonnes solutions. En revanche pour plusieurs problèmes, il existe très rarement une unique bonne solution.

<h1>Exemple 1</h1>

L'informatique de gestion tend à appliquer des règles métiers à des flux d'information.
Pour ce faire, il existe de multiples façons de procéder, par exemple : paramétrage de valeur en bases de données, moteur de règles, modélisation du métier dans l'applicatif (DDD).

Le conseil péremptoire qui va suivre a de fortes chances d'oublier certaines parties du problème :

<blockquote>
  mais si, mettez vos règles métiers dans une base de données. Comme ça on peut en rajouter au fur et à mesure, sans relivrer.
</blockquote>

Pourquoi pas, mais est-ce adapté dans notre cas ? Les règles sont-elles nombreuses ? complexes ? les deux ?
faut-ils les modifier fréquemment ? les remplacer ? les superposer ? les composer ? ont-elles un ordre de priorité ?

En fonction des réponses à ces questions, on pourra rechercher une approche qui répondra du mieux possible à notre problème.
Dans une de mes missions, nous avons codé un fichier drools paramétrant le menu d'une appli web. Ainsi le responsable opérationnel pouvait, en modifiant des dates dans ce fichier ou en spécialisant des catégories d'utilisateurs, masquer ou afficher certaines entrées du menu dépendant de la période de l'année.
C'était parfait pour le besoin étant donné qu'il n'y avait pas de possibilité d'anticiper sur les dates correspondants aux entrées. De surcroît, ce n'était pas lourd, à la portée d'un non-technicien et peu risqué pour le site.

Initialement, j'avais prévu de paramétrer ce menu via une base de données. Mais pour mettre à jour mon menu, il aurait fallu

<ul>
<li>ou créer une page d'administration sur le site, avec un rôle spécifique et les DAO et services de mise à jour, donc beaucoup de développement</p></li>
<li><p>ou bien créer des scripts de mise à jour, qu'il aurait fallu lier à des utilisateurs spécifiques pour la base de données qui avait des contraintes de sécurités différentes des miennes</p></li>
</ul>

<p>Ces solutions ne semblant pas adaptées en termes de coûts et/ou complexité au besoin, nous avons réfléchi et trouvé cette solution Drools.
Pour autant, si on me demande aujourd'hui

<blockquote>
  J'ai un problème de règles qui sont difficiles à mettre à jour. Qu'est ce qu'on peut faire ?
</blockquote>

Je ne répondrais pas forcément

<blockquote>
  Ok, je connais, on va utiliser Drools, c'est top pour ce genre de problématique !!!
</blockquote>

Pareil pour :

<blockquote>
  J'ai des problèmes de perf sur mon appli web au niveau des accès bases.
</blockquote>

La réponse n'est pas forcément

<blockquote>
  Bouge pas, j'ai ce qu'il te faut !! Avec hibernate et un cache aux petits oignons, ça va rouler tout seul.
</blockquote>

Avant de choisir une solution, il faut se demander quel est le problème à adresser (<a href="https://twitter.com/MrCafetux" title="MrCafetux">®MrCafetux rule#1</a> ).

Pour celà, il est sage de se demander quelle est la nature de ce problème ? qui est impacté ? à quelle fréquence ? comment est contourné le problème actuellement ? est-ce humain ? est-ce technique ? fonctionnel ? organisationnel ? est-ce vraiment important ? On le voit, l'arbre des questions est infini. Il n'y a donc pas de réponse toute faite, tout réflexe ne fait pas gagner du temps.

<h1>Exemple 2</h1>

Autre biais possible, en craftsmen consciencieux, on se tient au courant de la "mode" dans le monde du développement logiciel. Et il est très tentant (inconsciemment peut-être) d'essayer de résoudre un nouveau problème avec l'idée défendue pertinemment par tel ou tel cador/coach/gourou.

Alors que l'an dernier, tout le monde essayait de dockeriser son application, cette année, on essaye de la splitter en microservices, dans l'espoir que nos applications répondront ainsi au besoin, seront pérennes, scalables, évolutives, etc...
Mais dans les conférences, ou les articles, la culture d'entreprise n'est pas abordée. Bien souvent, cette culture est ce qui conditionne le plus la qualité de l'application.

<ul>
<li>Pour qui la scalabilité est un besoin réel ?</p></li>
<li><p>Qui à la capacité de mettre en place des équipes sachant appréhender ces nouvelles problématiques ?</p></li>
</ul>

<p>Peu de monde. Pour autant, même si l'on se sait incapable d'embrasser la totalité de ce paradigme, il ne faut pas rejeter en bloc ces idées. Il convient de se demander quelles en sont les parties qui pourraient nous être utiles, par exemple :

<ul>
<li>arrêter de couper les applications en couches,</p></li>
<li><p>utiliser le vocabulaire métier dans la modélisation,</p></li>
<li><p>extraire de l'application globale une partie plus gourmande en ressource que les autres,</p></li>
<li><p>etc etc...</p></li>
</ul>

<p>En résumé, n'embrassez pas la nouveauté parce que c'est nouveau et bien vu mais parce qu'elle permet de résoudre vos problèmes de manière plus légère qu'actuellement.

<h1>Exemple 3</h1>

Dernier exemple de mauvais réflexe : les excès de wiki.
Un wiki est un espace de collaboration où chacun peut participer à la hauteur de ses compétences.
C'est un outil formidable permettant d'obtenir des documentations souvent complètes s'adaptant aux différents points de vue des lecteurs. Et oui, chaque personne n'ayant pas compris un aspect de la documentation, peut rajouter ou expliciter le paragraphe incompris. Et ceci, très simplement.

Celà ne signifie pas pour autant que ce wiki doive devenir un espace de stockage que chacun s'approprie en y ajoutant ce qu'il pense manquer dans la doc principale.
Par exemple :

<ul>
<li>un tutoriel pour installer ruby sur son poste</p></li>
<li><p>une documentation sur les annotations en Spring</p></li>
</ul>

<p>Ces docs/tutoriels pullulent sur internet, souvent clairs avec des réponses aux questions générales, pourquoi en héberger un ersatz sur notre wiki ?

Il y a quelque mois, quelqu'un a mis en place un nouvel outil dans la mission, basé sur 2/3 techno et framework connus : ruby, coffeescript entre autres.
Celui-ci, crée un README à la racine du projet expliquant :

<ul>
<li>à quoi sert le projet</p></li>
<li><p>quelles sont les technologies utilisées</p></li>
<li><p>comment installer le projet</p></li>
<li><p>un guide de démarrage</p></li>
</ul>

<p>Le tout en moins de 50 lignes. Parfait.
On n'alourdit pas l'outil de documentation de toute la société en ajoutant une nouvelle doc, qui sera placée presque au hasard. De plus pas besoin de rechercher cette documentation, puisqu'elle est à la racine du projet.

Ici intervient un autre développeur souhaitant contribuer au projet. En lisant la doc, il installe les différents modules mais butte sur l'installation de la bonne version de tel ou tel outil. Après quelques recherches sur internet, il parvient à tout faire tourner.
Et là, il se dit :

<blockquote>
  Qu'est ce que j'ai galéré !!! Je vais coller ce README dans le wiki parce que c'est la documentation générale de l'entreprise et y ajouter un tutoriel pour montrer comment installer la version x de telle dépendance qui m'a posée problème.
</blockquote>

La volonté de partager son savoir et d'aider son prochain est un très bon réflexe. Mais encore faut-il réfléchir à comment l'aider le mieux.
En effet désormais :

<ul>
<li>on a 2 documentations dont on ne saura jamais laquelle est à jour sur telle ou telle partie</p></li>
<li><p>le wiki déjà touffu, la nouvelle doc ne sera pas simple à trouver et sera de plus, inutile à la majorité la société</p></li>
<li><p>on va ajouter dans le wiki de la doc technique déjà existante sur internet et dont on sait qu'elle sera dépréciée au prochain changement de version de telle ou telle dépendance.</p></li>
</ul>

<p>N'aurait-il pas mieux valu, dans le README :

<ul>
<li>décrire le problème rencontré ?</p></li>
<li><p>expliquer qu'il s'agit d'un problème de version de telle dépendance ?</p></li>
<li><p>préciser qu'on peut le résoudre en allant voir ici et là sur internet ?</p></li>
</ul>

<p>Ainsi, on a la doc toujours sous la main, qui guide, aide à comprendre et surmonter les éventuels écueils rencontrés.
Ce qui est bien plus formateur que de donner une solution sans explication.

<blockquote>
  Donne un poisson à un homme, tu le nourris pour un jour. Apprends-lui à pêcher, tu le nourris pour toujours.
</blockquote>

dis le dicton. C'est un peu le même principe ici.
Voici un exemple de bonne volonté réflexe devenue contre productive par manque de réflexion.

<h1>conclusion</h1>

A la lecture de ces lignes, j'imagine que quelques exemples vécus vous viendront à l'esprit, tel ce collègue qui crée un fichier pom avec Spring, Hibernate et un driver JDBC à chaque début de projet.

Mais alors, que faire ? Y a-t-il des signes trahissant un comportement compulsif ? Comment reconnait-on l'adhérent au <a href="https://en.wikipedia.org/wiki/Cargo_cult_programming" title="Cargo Cult Programming">culte du Cargo</a> ?

Mis à part me méfier instinctivement des collègues :

<ul>
<li>qui me disent <em>"YAGNI"</em> dès que je me pose des questions de modélisation,</p></li>
<li><p>qui énumèrent leurs réunions de la veille lors du Daily Meetup,</p></li>
<li><p>qui songent à rajouter une étape dans un process dès que l'équipe a rencontré un problème,</p></li>
</ul>

<p>je me méfie surtout des coéquipiers qui font ce qu'ont leur demande sans jamais remettre en cause ce qui leur est demandé ;)

Attention, aussi à ne pas sombrer dans l'excès inverse, tout remettre en cause et ne jamais décider. Michel Audiard disait

<blockquote>
  Un imbécile qui marche ira toujours plus loin que deux intellectuels assis.
</blockquote>

Je ne creuserais pas plus ces problématiques intéressantes ici. Avoir plus d'ambition à ce propos, serait vain en ces quelques lignes. Nous l'avons vu le sujet est vaste, les exemples nombreux et la bonne méthode n'existe pas ;)
