---
layout: post
title: Craft Conf à Budapest - le rendez-vous à ne pas manquer
date: 2014-05-15 11:09
author: nicolas fedou
comments: true
categories: [Actu, craft conf, Evénements, software craftsmanship]
---
<p style="text-align: justify;">J'ai eu la chance d'assister à une conférence malheureusement peu connue en France: <a href="http://craft-conf.com/2014/" target="_blank"> Craft Conf à Budapest du 23 au 25 avril</a>, une conférence de deux jours sur le Crafstmanship précédée par différents workshops d'une journée chacun...
Je vous invite à lire leur définition du compagnonnage/artisanat sur leur site web.</p>

<p style="text-align: justify;">Leurs speakers sont presque tous de grands noms dans leurs domaines. Voici un extrait traduit de leur site web :</p>

<blockquote>
<p style="text-align: justify;">La liste de speakers inclus Douglas Crockford, qui déteste être appelé le père du JavaScript, Bruce Eckel, l'auteur de livres parmi les plus populaires sur Java et C++, Dan North, qui est un expert de l'agile mondialement connu, Gojko Adzic, qui est un expert dans la production de logiciels de haute qualité, Jeff Hodges, qui construit des systèmes distribués chez Twitter et qui nous apprendra comment les faire, John Willis, qui est le père de devops et qui nous parlera des défis à venir dans ce domaine, Michael Feathers, qui a écrit une de nos bibles, Mitchell Hashimoto, le créateur d'outils populaires comme Vagrant, Packer et SERF, ou encore Michael Nygard, qui est l'un des speakers les plus populaires dans le domaine des architectures résilientes, et Jonas Boner qui nous apprendra pourquoi les applications réactives sont importantes et comment nous devrions les écrire.</p>
</blockquote>

<p style="text-align: justify;">Bref, du beau monde.</p>

<p style="text-align: justify;">Craft Conf était organisée par <a href="http://www.prezi.com" target="_blank">Prezi</a> et Ustream. Ils ont mis en place une bonne organisation avec un maître de conférence (MC) dans chaque salle qui posait les questions récoltées sur sli.do pendant les sessions. Ils ont ainsi évité de balader le micro dans toute la salle et ils ont posé en premier les questions ayant le plus de votes, es question populaires ayant 5 votes même en plénières (keynotes).
C'est la première fois que je voyais des présentations diffusées en direct sur UStream et du wifi pour les participants. Ce dernier point semble un luxe, mais quand on pose les questions sur internet et que l'on se trouve à Budapest, on est content de ne pas s'abonner à un service de données mobiles européen...
D'une part, la diffusion en direct est toujours accessible, d'autre part, les présentations sont en post-production.
Enfin, il y avait un panneau avec post-its pour donner un retour sur la conférence elle-même et un autre sur les offres d'emplois dans toute l'Europe y compris Paris avec Arolla et Betclic !</p>

<p style="text-align: justify;">Bref, bien organisé.</p>

<p style="text-align: justify;">A cet instant de l'article, soit vous êtes sur les pages web de la craft conf pour trouver le programme et choisir vous-même les présentations que vous allez voir (Il n'y a rien à la télé ce soir !!!), soit vous lisez encore ces lignes et un résumé ou une vue d'ensemble vous suffit.</p>

<h1 style="text-align: justify;"><strong>Résumé des présentations remarquables</strong></h1>

<h2 style="text-align: justify;"><a href="http://t.co/ZtAJOfa6Zi" target="_blank">Programming only better</a></h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/bodil" target="_blank">Bodil Stokke</a></li>
</ul>

<p style="text-align: justify;">Bodil ne propose pas de solutions mais décrit bien notre gros problème : la complexité. Elle augmente exponentiellement avec le volume de code. En POO, nos objets conservent un état et il suffit de louper une opération de mutation pour perdre le fil du spaghetti. La programmation fonctionnelle y répond avec ses arguments immutables... Mais il reste le fait que pour des entrées non-testées, nous n'avons aucune garantie du résultat. De plus, les structures de contrôle ne sont maîtrisables qu'en dehors de toute concurrence.
Résultat, s'il nous faut un langage sans état ni structure de contrôle, il ne nous reste plus que les langages déclaratifs...
Sa présentation est à voir car elle fait réfléchir au volume et à la complexité de nos codes sur fond de poneys roses et pas toujours contents et avec <strong><a href="https://github.com/bodil/only-better/" target="_blank">du live coding dans les slides</a></strong> !</p>

<h2 style="text-align: justify;">Databases &amp; Schemas in an Agile World</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/andrewgodwin" target="_blank">Andrew Godwin</a></li>
</ul>

<p style="text-align: justify;">Andrew est un commiter du projet Django et s'occupe de migrations de schémas de base de données relationnelles. Il constate que Postgres est clairement en avance sur les migrations de schéma à chaud et en concurrence, Mysql peut le faire en lecture seule et pose des verrous sur des tables, là où Oracle demande une déconnexion et une reconnexion. Les base de données NoSQL ou ayant un schéma implicite n'ont pas d'obligation de migration, mais elles font des erreurs silencieuses... Contre cela, si le schéma n'est pas dans la base, il doit être dans le code. Enfin, on se rend compte avec les bases qui communiquent en JSON que l'on peut stocker des données structurées pour les champs sur lesquels on fait des recherches et stocker le reste dans un format binaire dans un blob. Le polyglot data sera pour une autre présentation. En attendant Postgres 9.2 contient un type de colonne JSON et la 9.4 proposera des recherches sur la présence de clefs et des clauses de recherche sur des valeurs d'objets JSON.
On peut ainsi réduire le nombre de modifications de schémas et obtenir le meilleur de deux mondes en performance de requêtes de recherche et d'extraction de données. Mais on aura aussi les deux contraintes migration de schéma et de schéma dans le code.</p>

<h2 style="text-align: justify;"><a href="http://www.codingthearchitecture.com/presentations/craftconf2014-agility-and-the-essence-of-software-architecture" target="_blank">Agility and the essence of software architecture</a></h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/simonbrown" target="_blank">Simon Brown</a></li>
</ul>

<p style="text-align: justify;">Le développement agile d'un logiciel demande une architecture agile. Une architecture logicielle est l'ensemble des choix technologiques difficiles à changer plus quelques détails. En fait, une architecture n'est pas agile, mais <strong>une bonne architecture permet l'agilité</strong> quelque part entre le plat de spaghettis monolithique et les micro-services.
Un mot sur l'agilité :</p>

<blockquote>
<p style="text-align: justify;">Si on délivre nos applications plus vite que les spécifications changent, nous sommes suffisamment agiles.
L'architecture dépend des choix technologiques et doit permettre l'agilité, il faut donc une structure capable d'ouvrir les technologies choisies aux futures évolutions de notre logiciel. Cet structure n'est pas évidente, doit être trouvée, mais aussi défendue. Pour cela, il faut pouvoir la décrire et s'en servir.
Pour la décrire, il dessine trois ou quatre types de schéma. Peu importe le type de dessins (Uml, boîtes à flèches, ...) tant que tout le monde est d'accord.
Le premier dessin est un contexte du système à réaliser avec les entrées et sorties du système, les acteurs et les dépendances.
Le deuxième est un conteneur où le système s'exécute avec les choix technologiques, et l'aspect global de ce système, etc.
Le troisième est sur les composants logiques et comment ils interagissent entre eux et avec le conteneur.
Enfin, parfois les classes implémentant les détails d'un composant logique méritent leurs propres dessins.
Une fois que nous avons des bases permettant de délivrer du logiciel, il faut écrire un code qui reflète cette architecture en utilisant le même langage et en organisant le code en composants, etc... Ainsi, un composant logique est un package Java ayant le même nom dans le code et dans les schémas. Si les développeurs ne font pas le lien entre le code et l'architecture, cette dernière va disparaître.</p>
</blockquote>

<h2 style="text-align: justify;"><a href="https://speakerdeck.com/tastapod/jackstones-the-journey-to-mastery" target="_blank">Jackstones: the journey to mastery</a></h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/tastapod" target="_blank">Dan North</a></li>
</ul>

<p style="text-align: justify;">Après <a href="http://craft-conf.com/2014/#workshops/DanNorth" target="_blank">un atelier qu'il a animé la veille de la conférence</a>, Dan North décrit quelques idées qui l'ont suivi pendant sa vie professionnelle d’artisan du logiciel (Software Craftsman).
Il nous a montré que maîtriser son domaine se traduit par différentes qualités et différents efforts selon les domaines comme le pianiste, le soldat, ... Une définition générique de la maîtrise peut être :</p>

<blockquote>Une capacité dans un contexte

une capacité à faire quelque chose, quand le contexte permet de l'appliquer.</blockquote>

<p style="text-align: justify;">Une mesure de cette maîtrise peut être celle d'un professeur de tennis :</p>

<blockquote>La performance est le potentiel moins les interférences</blockquote>

<p style="text-align: justify;">Dans le logiciel, la maîtrise peut être assez variée : du beau code, un logiciel qui ai de l'impact, ...
Pour s'améliorer dans les domaines qui nous intéressent, il vous faut trouver un mentor qui fait ce que vous voulez apprendre. Et copiez-le, apprenez de lui, faites semblant, jusqu'à ce vous le fassiez vraiment.</p>

<p style="text-align: justify;">Essayez de ne résoudre que les vrais problèmes, ne perdez pas de temps sur de fausses contraintes. Apprenez les bases, les théories et les méthodes : Solid, tdd, xp, ...
Maximisez vos logiciels pour obtenir des retours du logiciel lui-même, mais aussi des gens (utilisateurs, testeurs, ...).
N'hésitez pas à essayez d'autres approches, d'autres domaines.
Découvrez comment vous apprenez et découvrez comment vous pratiquez de nouvelle techniques.
Une fois que vous avez un niveau, n'oubliez pas que vous ne l'avez pas toujours eu : conserver de l'humilité.
Et enfin, l'apprentissage, le voyage ne se termine jamais !</p>

<h2 style="text-align: justify;">Responsibly maximizing craftsmanship in software engineering</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/postwait" target="_blank">Theo Schlossnagle</a></li>
</ul>

<p style="text-align: justify;">L'idée de cette présentation est de maximiser la livraison de logiciels de qualité. Une difficulté est qu'un code de qualité fonctionne, du coup, personne ne le regarde puisqu'il n'a pas besoin de correctifs. C'est comme s'il n'existait pas. Une autre est l'empilement de logiciels où Java script tourne dans une vm d'un navigateur qui tourne sur un Windows, qui tourne sur du code de drivers, ensuite viennent le code des routeurs, etc. Ils doivent tous fonctionner correctement !</p>

<p style="text-align: justify;">Pourquoi on produit si souvent de mauvais logiciels ?</p>

<ul style="text-align: justify;">
    <li>Les spécifications sont difficiles à écrire.</li>
    <li>Les projets sont longs et tout change, les spécifications, les environnements, les utilisateurs, ...</li>
    <li>La fainéantise n'est pas une vertu : automatiser avec un outil évite les erreurs humaines, utiliser un mauvais outil a l'effet inverse.</li>
</ul>

<p style="text-align: justify;">Qu'est ce qu'on peut y faire ?
Les projets complexes ont tendance à échouer. La dette technique n'est pas linéaire et grossir développe rapidement une inertie dangereuse.
Comme tout notre contexte change, nous devons avoir des logiciels capables de changer. Et joindre des composants de taille raisonnable que l'on peut jeter et ré-écrire et bien plus résistant au changement qu'un seul composant géant.
Pour trouver une bonne taille, le premier truc est d'apprendre à respecter ses échéances, tenir les délais de nos estimations. Un deuxième est de se renseigner sur les papiers académiques, les librairies qui résolvent nos problèmes, voire les collègues qui l'on peut-être déjà écrit dans le projet d'à coté. Un troisième est d'avoir des ingénieurs autonomes. Pas autonomes sur l'objectif, mais autonomes sur l'approche.
Comme nous avons un contrat social entre citoyens, nous devons prendre soins de nos APIs : domaine minimal, clair, stable, ...
On ne refactore pas un composant cassé, on le remplace.</p>

<h2 style="text-align: justify;">Find the Right Abstraction Level for Your Tests</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/gerardmes" target="_blank">Gerard Meszaros</a></li>
</ul>

<p style="text-align: justify;">Pour obtenir un retour sur investissement dans nos tests, il nous faut des tests verts, mais aussi qui décrivent le comportement attendu et qui résistent aux changements. Les tests unitaires prennent tous les détails en compte d'une toute petite portion de code qui doit être la plus petite possible. Au dessus, on trouve les test de transactions qui n'en vérifient qu'une en essayant de réduire à la fois la quantité de code testé et le niveau de détails d'implémentation. Et tout en haut, on trouve le test de workflow qui vérifie plusieurs use-case et transactions, bref, une grande surface de code sans se soucier des détails.
Ensuite, il prend un exemple de test de workflow et le simplifie avec nous. Enfin, il retravaille des tests que l'on trouve au quotidien pour en faire du code métier... On y a vu deux métiers, d'ailleurs : celui du logiciel testé et celui du test (Customer anyCustomer = givenAnyCustomer();)</p>

<h2 style="text-align: justify;">Data-Driven Software Engineering</h2>

<ul style="text-align: justify;">
    <li>Jevgeni Kabanov</li>
</ul>

<p style="text-align: justify;">Ce CEO de zeroturnaround nous fournit les résultats de <a href="http://zeroturnaround.com/rebellabs/complete-this-3-minute-java-tools-technology-survey-and-well-donate-cash-to-charity/" target="_blank">leur sondage</a> sur le respect des coûts, qualités et délais en corrélation avec les outils mis en places (CI, CD, pair programming)</p>

<h2 style="text-align: justify;"><a href="https://speakerdeck.com/garethr/continuous-integration-for-infrastructure" target="_blank">Continuous integration for Infrastructure</a></h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/garethr" target="_blank">Gareth Rushgrove</a></li>
</ul>

<p style="text-align: justify;">L'intégration continue permet de fusionner le travail de chaque développeur dans un lieu commun où l'on teste que l'ensemble continue de bien fonctionner. Avec l'"infrastructure as code", les outils de devopset les outils de cloud, nous développons de l'automatisation pour nos infrastructures. Lorsqu'une équipe entière s'occupe de ces développements, leur intégration en continue devient pertinente. Gareth travaille pour le gouvernement britannique où il fait face à cette problématique avec des outils et des idées.</p>

<h1 style="text-align: justify;"><strong>Celles que je souhaite voir en ligne</strong></h1>

<h2 style="text-align: justify;">Polyglot data</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/gregyoung" target="_blank">Greg Young</a></li>
</ul>

<p style="text-align: justify;">La présentation de Greg Young commence par dire que toutes les solutions de persistance sont nulles ! Chaque solution est bonne à quelque chose et mauvaise hors du cadre pour laquelle elle a été conçue. Choisir une solutions implique une représentation des données, un modèle. Choisir un mauvais modèle peut apporter une complexité inutile. Il avait ainsi aidé une startup à réduire son temps de développement car l'entreprise n'avait pas vu que son problème se représentait bien avec un graphe et pas avec un schéma relationnel.
Comme il n'est pas rare que nos applications ait un vaste panel de fonctionnalités et que tout change autour de notre logiciel, il n'est pas rare d'avoir besoin de plusieurs modèles pour satisfaire plusieurs type de fonctionnalités.
Greg présente alors l'event sourcing comme une solution permettant de traiter chaque événement du système en maintenant l'état de plusieurs modèles en cohérence. JTA et les transactions distribuées sont plus complexes et plus chères pour le système. L'event sourcing implique un "event store" qui est un simple stockage des événements et souvent un modèle conçus pour les vues de l'IHM. On peut avoir ensuite une série de modèles pour de l'audit, pour du suivi des clients, etc...</p>

<h2 style="text-align: justify;">How I Learned to Stop Worrying and Love Flexible Scope</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/gojkoadzic" target="_blank">Gojko Adzic</a></li>
</ul>

<p style="text-align: justify;">Comment convaincre que l'agile permettant de livrer des changements, il permet aussi de changer le périmètre fonctionnel de la prochaine livraison ?</p>

<h2 style="text-align: justify;">Testing the Hard Stuff and Staying Sane</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/rjmh" target="_blank">John Hughes</a></li>
</ul>

<p style="text-align: justify;">La concurrence et les systèmes distribués sont difficiles à tester, voire intestables. Essayer de couvrir ce genre de cas rend fou. Voici quelques techniques pour réussir à se concentrer sur ce que le code doit faire et pas sur ces cas tordus.</p>

<h2 style="text-align: justify;">Architecture War Stories</h2>

<ul style="text-align: justify;">
    <li><a href="https://twitter.com/stilkov" target="_blank">Stefan Tilkov</a></li>
</ul>

<p style="text-align: justify;">Comment de bonnes idées de bonnes personnes comme nous peuvent conduire à un désastre ? Voici quelques exemple tirés de faits réels.</p>
