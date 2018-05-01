---
layout: post
title: "Devoxx France 2012: J'y étais !"
date: 2012-04-30 12:22
author: arnauld loyer
comments: true
categories: [cqrs, développement, devoxxFR, elasticsearch, Evénements, GIT, green, hibernate, html5, java, javascript, jvm, nodeJS, Programmation]
---
<img class="alignnone size-full wp-image-456" style="margin: 0 auto;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/Devoxx-Flyer.png" alt="DevoxxFrance flyer" />

<h1><img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/DevoxxFrance2012-200x14.png" alt="Devoxx France 2012" /> ~ J'y étais!</h1>

<p style="text-align: justify;">Tout d'abord, il convient d'adresser un grand merci et un grand bravo aux organisateurs et aux équipes techniques. Un super évènement très dense !</p>

<p style="text-align: justify;">Voici un retour sur les conférences auxquelles j'ai pu assister pendant deux jours. Toutes les conférences ayant été enregistrées, je ne saurais que trop vous conseiller de les voir ou revoir sur <a href="https://www.youtube.com/user/Parleysdotcom">Parleys</a> dès qu'elles seront disponibles.</p>

<h1>Jeudi [Keynotes]</h1>

<p style="text-align: justify;">Après un rappel - chargé d'émotion pour les organisateurs - sur la genèse de Devoxx France par Antonio Goncalvez (<a href="http://twitter.com/#!/agoncal">@agoncal</a>), la keynote suivante me laisse perplexe.</p>

<p style="text-align: justify;">"Fier d'être développeur?" par Pierre Pezziardi</p>

<img style="float: right;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/InformatiqueConviviale.png" alt="Informatique conviviale" width="140px" />

<p style="text-align: justify;">Peut-être que je n'étais pas très bien réveillé mais je n'ai pas du tout réussi à entrer dans le contenu de cette présentation. Je trouvais très difficile de voir où voulait en venir M. Pezziardi, j'avais plus l'impression d'un discours décousu et un patchwork d'idées (qui mérite d'être creusées!) livrées pelle-mêle.</p>

<p style="text-align: justify;">Je suis fier d'être développeur mais je ne me suis pas trouvé dans cette conférence et après réflexion je n'étais pas dans le bon état d'esprit: cette présentation est tirée de son livre "Lean Management". Le titre reflète très bien le contenu de cette présentation contrairement à "Proud to be a developer".</p>

<strong>Ceci dit, j'ai investi dans ce livre afin de "revoir" cette présentation avec un autre angle.</strong>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-3s5.png" alt="ROTI 3/5" />

<h3 style="margin-top: 5em; clear: both;">"This could be Heaven or this could be Hell"</h3>

<a href="http://twitter.com/#!/ludoch/status/192892433456050176/photo/1"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ThisCouldBeHellOrHeaven-tweetPic.jpg" alt="Ludovic Champenois's picture: Ben Evans &amp; Martijn Verburg!" width="500px" /></a>

<p style="text-align: justify;">Un super show de Ben Evans &amp; Martijn Verburg! (<a href="http://twitter.com/#!/kittylyst">@kittylyst</a> &amp; <a href="http://twitter.com/#!/karianna">@karianna</a>) sur ce qui pourrait arriver de mieux (ou de pire !) à Java.
Un vrai spectacle à voir ne serait-ce que pour réaliser que rien n'est tracé et que tout peux changer: Développeurs, investissez-vous et donnez rapidement vos retours sur les prochaines évolutions de la JVM.</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] ElasticSearch: moteur de recherche NoSQL/REST/JSON</h2>

<p style="text-align: justify;">Tout part du constat qu'une recherche textuelle en SQL voit son temps d'exécution dégénérer lorsque les tables dépassent les 500k lignes:</p>

<p style="text-align: justify;"><code>select document_id from documents where text like '%devoxx%'</code>.</p>

<p style="text-align: justify;">Compte tenu des volumes impliqués, il est alors nécessaire d'avoir une solution de recherche plus poussée.
Elle doit intégrer à la fois :</p>

<ul style="text-align: justify;">
    <li>l'indexation des données pour une recherche performante</li>
    <li>la gestion de gros volume de données via un mode distribué: ce qui implique réplication des données sur plusieurs nœuds, sharding, tolérance aux pannes, montée en charge par ajout de ressource...</li>
    <li>une gestion souple des différents modèles de document</li>
</ul>

<p style="text-align: justify;">"Pas évident à trouver un truc comme ça" <em>vous allez me dire!</em></p>

<p style="text-align: justify;">Imaginez <a href="http://lucene.apache.org/core/">Lucene</a> avec une interface REST et un support complet pour le mode distribué! <em>Hummmm je vous sens intéressés mais dubitatifs!</em>: "ça doit être drôlement compliqué à installer et configurer!".</p>

<p style="text-align: justify;">Imaginez une application que vous téléchargez et sans configuration, vous avez tout ça prêt à l'emploi: <em>perplexe?</em></p>

<p style="text-align: justify;">Et bien, c'est pourtant le constat qu'a fait David Pilato (<a href="http://twitter.com/#!/dadoonet">@dadoonet</a>) en découvrant <a href="http://www.elasticsearch.org/">ElasticSearch</a> et qu'il nous livre à travers cette présentation aussi claire, fluide et simple que le produit qu'il présente.</p>

<p style="text-align: justify;"><em>N.B.: si vous ne connaissez pas Lucene autrement que par son nom, je vous conseille la présentation "Recherche d'information" à laquelle j'ai assisté vendredi.</em></p>

<p style="text-align: justify;">Toutes les interactions se font par requête HTTP+Json, pas d'interface utilisateur sexy: c'est un moteur ! On peux donc tout faire par simple ligne de commande (par exemple via <code>curl</code>).
Des urls de type REST permettent très simplement de définir le typage de nos données, l'espace logique auquel elles appartiennent (index) et les actions que l'on souhaite effectuer: création, suppression, recherche, etc.</p>

<div style="margin: 0 auto;">

<img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ElasticSearch-slide.png" alt="ElasticSearch REST uri" />

<em><a href="http://www.slideshare.net/dadoonet/elasticsearch-devoxx-france-2012">Les slides</a></em>

</div>

<p style="text-align: justify;">Il est ensuite assez simple de naviguer à travers nos données à la manière d'un drill-down en définissant les "facettes" lors des requêtes. Ces facettes <em>ressemblent</em> aux "group by" que l'on trouverait sur une base de type SQL.</p>

<p style="text-align: justify;">PS: la présentation était tellement simple que le lundi d'après, mon client ne jurait plus que par cela, et voulait absolument que l'on fasse quelques POC (Proof Of Concept) pour nos cas d'utilisations.</p>

<blockquote class="twitter-tweet" lang="fr">La présentation Elasticsearch pour <a href="https://twitter.com/search/%2523devoxxfr">#devoxxfr</a> est disponible sur slideshare <a title="http://www.slideshare.net/dadoonet/elasticsearch-devoxx-france-2012" href="http://t.co/pk464YnT">slideshare.net/dadoonet/elast…</a>

— David Pilato (@dadoonet) <a href="https://twitter.com/dadoonet/status/193443130509504513">Avril 20, 2012</a></blockquote>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" /> <img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/AVoir.png" alt="A voir!" />

<h2 style="margin-top: 5em; clear: both;">[Conf] NodeJS and Javascript Everywhere</h2>

<p style="text-align: justify;">Une belle présentation de NodeJS avec du codage en direct d'un serveur proxy en une vingtaine de lignes de javascript et une pincée de texan par Matthew Eernisse (<a href="http://twitter.com/#!/mde">@mde</a>) !</p>

<p style="text-align: justify;">Le dernier slide "Try it; you'll like it" résume très bien l'impression que l'on a en ressortant, quand bien même je regarde NodeJS depuis plusieurs mois déjà !</p>

<p style="text-align: justify;">Plusieurs bonnes pratiques pour la gestion de l'asynchronisme et des erreurs inhérentes, en vrac : <code>uncaughtException</code> handler et preemptive timeout afin d'informer l'utilisateur si une erreur à eu lieu.</p>

<pre>// un garde fou est programmé afin que l'utilisateur
// ait un retour en moins de 600ms quoiqu'il arrive
// comme la baleine bleu de Twitter

// pas plus de 600ms pour avoir un retour
// sinon on génère une erreur 500
var timeout = 600;
var handler = setTimeout(function() {
    var errorCode = 500;
    response.writeHead(errorCode, "Oops something bad happened");
}, timeout);

...

// si tout va bien on annule notre garde fous
clearTimeout(handler);</pre>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Client/Server Apps with HTML5 and Java</h2>

<p style="text-align: justify;">Une chouette présentation par James Ward (<a href="http://twitter.com/#!/_JamesWard">@_JamesWard</a>) sur ce que devrait être les applications Web modernes. Cette présentation est une introduction au framework play et à une approche de type WOA (Web Oriented Architecture): stateless, restful et asynchrone.</p>

<p style="text-align: justify;">Le discours tourne autour d'une idée fondamentale: "The browser as an application platform".
Il est désormais important d'exploiter au maximum les possibilités du navigateur et de placer beaucoup plus de logique sur celui-ci, de nombreux frameworks javascript intègrent déjà ces notions.</p>

<p style="text-align: justify;">Enfin, James Ward nous parle de <a href="https://github.com/webjars">webjar</a> dont le but est d'exploiter maven pour la distribution des librairies javascript.</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" />

<h2 style="margin-top: 5em; clear: both;">[Quickie] C'est si simple GIT</h2>

<p style="text-align: justify;">Une présentation très (trop !) rapide de quelques possibilités de GIT à travers l'utilisation de plusieurs repositories: un central et deux développeurs. Les développeurs n'ont pas les droits de commit sur le repository central et doivent donc propager (push) leurs commits, qui seront ensuite intégrer par l'administrateur.</p>

<p style="text-align: justify;">La présentation nous montre comme il est facile pour un développeur de partager sa branche de travail directement sur le repository de son collègue, travailler ensemble sur la résolution d'un bug au travers de plusieurs commits. Puis, finalement de les fusionner (squash) en un seul et unique commit, de le pousser sur le repository central, et permettre au responsable de l'intégrer au répository central après vérification.</p>

<p style="text-align: justify;">On regrettera l'utilisation abusive d'alias, de 'clear console' empêchant d'observer les résultats, et le côté schizophrène de la console qui joue tour à tour les trois rôles: les deux dev. et le responsable. Il est nécessaire de connaitre un peu GIT au préalable pour bien apprécier cette présentation.</p>

<blockquote class="twitter-tweet" lang="fr">Mon Quickie "C'est si simple GIT !" à Devoxx France 2012 disponible sur <a href="https://twitter.com/search/%2523github">#github</a> <a title="https://github.com/ulrich/devoxx_france_c_est_si_simple_git" href="https://t.co/6XtHNVzq">github.com/ulrich/devoxx_…</a> <a href="https://twitter.com/search/%2523devoxxfr">#devoxxfr</a>

— ulrich (@ulrich) <a href="https://twitter.com/ulrich/status/194187563173556224">Avril 22, 2012</a></blockquote>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] DevOps : pas que pour gérer des serveurs</h2>

<p style="text-align: justify;">Un retour d'expérience sur l'installation de poste de formation par Jérôme Bernard (<a href="http://twitter.com/#!/jeromebernard">@jeromebernard</a>).
Jérôme nous présente sa problématique: installer automatiquement des dizaines de postes de formation automatiquement afin d'avoir les environnements et les logiciels nécessaires pour chaque type de formation, mais aussi pour les consultants en inter-contrat. Les postes étant "mal-traités" par les formations, il est nécessaire de permettre de les réinitialiser avant chaque début de formation.</p>

<p style="text-align: justify;">Plusieurs solutions ont été évoquées et celle retenue consiste à installer sur chaque poste des machines virtuelles Virtual Box. Ces machines virtuelles sont disponibles sur le réseau et automatiquement propagées sur chacun des postes. Les machines virtuelles sont alors lancées et automatiquement installées et configurées en fonction de leurs propres paramètres.</p>

<div style="width: 100%; margin: 0 auto;">

<img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/DevOps-PasQuePourlesServeurs.png" alt="DevOps : pas que pour gérer des serveurs" />

<em><a href="http://www.jerome-bernard.com/blog/2012/04/20/devops-presentation-at-devoxx-fr/">Les slides</a></em>

</div>

<p style="text-align: justify;">Un serveur <a href="https://www.chef.io/chef/">Chef</a> est responsable d'installer sur chaque machine Virtual Box. La machine virtuelle à utiliser est quant à elle installée et déployée par <a href="http://vagrantup.com/">Vagrant</a>. Ensuite, la machine virtuelle elle-même dispose de sa propre installation de <a href="https://www.chef.io/chef/">Chef</a> (en mode solo) pour se configurer et se mettre à jour.</p>

<p style="text-align: justify;">Comme chaque machine virtuelle se réinstalle, il important aussi de disposer de miroirs pour éviter de surcharger la bande passante, ainsi les installations téléchargent directement depuis un serveur interne, et la connexion internet n'est pas saturée.</p>

<p style="text-align: justify;">De très bonnes idées pour mettre en place automatiquement des environnements d'intégration continue impliquant de configuration variée de base de données, de serveur d'application, etc.</p>

<blockquote class="twitter-tweet" lang="fr">Mes slides de la présentation <a href="https://twitter.com/search/%2523DevoxxFR">#DevoxxFR</a> sur le DevOps pour autre chose que des serveurs sont en ligne : <a title="http://www.jerome-bernard.com/blog/2012/04/20/devops-presentation-at-devoxx-fr/" href="http://t.co/7TE5a5O3">jerome-bernard.com/blog/2012/04/2…</a>

— Jérôme Bernard (@jeromebernard) <a href="https://twitter.com/jeromebernard/status/193234747709865984">Avril 20, 2012</a></blockquote>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Behind the Scenes of Day-To-Day Software Development at Google</h2>

<p style="text-align: justify;">Hummmm... leurré l'aura de Google... une présentation assez décevante de Petra Cross (<a href="http://twitter.com/#!/petracross">@petracross</a> du fonctionement des équipes de développements chez Google. Après un rappel de la philosophie Google <a href="http://www.google.com/about/company/philosophy/">Ten things we know to be true</a> On enchaine avec les différents postes hiérarchiques et casquettes que l'on peut être amené à porter: les Team Leader, Product Manager, ... Les équipes ne sont jamais vraiment figées, mais constituées à chaque projet en prenant des gens à gauche à droite en fonction des besoins du projet; un teamleader est un développeur comme les autres avec des meetings en plus (charge supplémentaire de préparation des Stories...)</p>

<p style="text-align: justify;">Beaucoup de temps passé sur la présentation des méthodes: waterfall, spirale et agile. Pour finalement dire que Google fait un mix des trois, et encore que chaque équipe décide un peu de son fonctionnement propre. On retrouve quand même une forte empreinte de scrum: itération courte, story évaluée en points, découpage en tâches, poker planning, ...</p>

<ol>
    <li style="text-align: justify;">Pas de daily standup meeting ! Ça ne sert à rien puisque tout est déjà écrit sur le tableau des tâches: ce qu'il reste à faire, ce qui est en cours et ce qui est fait.</li>
    <li style="text-align: justify;">Les tâches à faire sont priorisées et il faut obligatoirement prendre celle du-dessus, la plus prioritaire, mettre son nom dessus et la changer de colonne.</li>
    <li style="text-align: justify;">Toutes les tâches qui pourraient arriver en cours d'itération sont placées dans le "glaçon" avec un "oui-oui t'inquiètes on la met là et dès qu'on aura du temps on le traitera". Ce qui n'arrive généralement jamais compte-tenu de la règle 2.</li>
    <li style="text-align: justify;">Pas de rétrospective après chaque itération, mais toutes les 2 ou 3 itérations.</li>
</ol>

<p style="text-align: justify;">Enfin, quelques éléments intéressants (dernier 1/4h) pour le chiffrage des stories lors des Poker planning. En cas de désaccord - supposons que les chiffrages soient 2,2,3,8 - seule une personne de chaque extrême (une pour les 2 et celle du 8 ) a le droit de parler en suivant une formulation bien précise indiquant les étapes identifiées pour faire la tâche: "Pour faire X, il faut faire a,b et c".
Une fois que les deux personnes se sont exprimées, second tour de vote. Si un désaccord subsiste, il est alors possible de poser des questions, mais là aussi en suivant des règles strictes:
* les questions doivent être uniquement binaires (oui ou non) ou à choix multiples,
* les questions ne doivent pas contenir d'éléments émotionnels pouvant influencer les réponses: par exemple "ce n'est pas trop difficile de faire ça?" est interdit.</p>

<p style="text-align: justify;">En bref, les développeurs développent et expédient au plus vite toutes les autres tâches et les interactions humaines...</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-2s5.png" alt="ROTI 2/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Pour un développement durable</h2>

<p style="text-align: justify;">Dernière conférence de la journée, par Frédéric Dubois, elle ne m'a pas laissé de souvenirs marquants, je regarde mes notes: le logo de la conduite accompagnée... le développeur doit se prendre en main et doit être valorisé dans l'entreprise, il doit être "Fier d'être développeur".</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-qs5.png" alt="ROTI ?/5" />

<h1></h1>

<h1></h1>

<h1></h1>

<h1>Vendredi</h1>

<h2 style="margin-top: 5em; clear: both;">[Keynote] Abstraction distraction</h2>

Juste à temps pour la keynote de Neal Ford (<a href="http://twitter.com/#!/neal4d">@neal4d</a>): à voir absolument ! et pas que les développeurs.

Abstraction Distractions: les slides

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" /> <img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/AVoir.png" alt="A voir!" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Web Oriented Architecture, une transmutation des pratiques de construction des SI</h2>

<p style="text-align: justify;">La WOA par Habib Guergachi (<a href="http://twitter.com/#!/hguergachi">@hguergachi</a>: wahoooooo! quel show ! 55min de spectacle ponctué par de nombreux applaudissements.
C'est quoi l'avenir des applications ? Certainement pas les appels synchrones et l'approche RPC et stateful. Il y a trois solutions pour construire la prochaine application: construire avec les briques du passé, construire avec les briques de maintenant, une application qui sera dépassée en sortant, ou finalement construire le futur avec les technologies de demain.</p>

<p style="text-align: justify;">JEE est obsolète ! Les mentalités doivent changer, l'avenir est à la WOA: HTML + URI + HTTP. Les applications doivent être asynchrones, RESTful et stateless. Et surtout, il faut être ... relax ! "la relaxation temporelle" !
Il faut découper les applications en fournisseur de ressources. Lorsqu'une ressource est fournie, les ressources liées sont identifiées par leur URI. On découvre aussi un nouvel acronyme <a href="http://en.wikipedia.org/wiki/HATEOAS">HATEOAS</a>.</p>

<p style="text-align: justify;">Au final, on retrouve tous les concepts distillés dans les principes REST (voir <a href="http://opikanoba.org/tr/fielding/rest/">Thèse de Roy T. Fielding - Traduction du Chapitre 5 : REST</a> et <a href="http://www.amazon.fr/Restful-Web-Services-Leonard-Richardson/dp/0596529260/ref=sr_1_1?ie=UTF8&amp;qid=1335391381&amp;sr=8-1">Restful Web Services</a>) mis dans un spectacle, vivant, drôle et surtout accessible !</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Hibernate OGM: JPA pour NoSQL</h2>

<p style="text-align: justify;">Après un rappel sur les différents types de base NoSQL, Emmanuel Bernard (<a href="http://twitter.com/#!/emmanuelbernard">@emmanuelbernard</a>) nous présente Hibernate OGM: un projet en cours de développement permettant de réutiliser les annotations JPA pour construire une représentation adaptée au stockage NoSQL utilisé.
Un exemple bluffant ou juste en changeant les paramètres de persistance, la même application tourne indifféremment avec une base de données, Infinispan ou MongoDB.</p>

<p style="text-align: justify;"><a href="http://ogm.hibernate.org">Hibernate OGM</a> définit un ensemble de mapping équivalent selon le système de persistance sous-jacent, en créant par exemple des enregistrements pour toutes relations entre entités.</p>

<p style="text-align: justify;">Un projet qui me laisse dubitatif:</p>

<ul style="text-align: justify;">
    <li>Avec Hibernate, l'utilisateur est maître de son schéma de base de données, et configure ses entités afin de les faire correspondre aux tables sous-jacentes alors qu'Hibernate OGM décide du format de stockage et de sa représentation dans la base NoSQL.</li>
    <li>On réutilise les mêmes annotations pour des paradigmes de stockage bien différents, et le risque d'avoir un modèle non adapté et non optimisé peut être grand.</li>
</ul>

<p style="text-align: justify;">Un projet à suivre mais qui risque d'être rapidement dépassé par des solutions spécifiques à chaque type de base NoSQL et intégrant leurs spécificités comme les fonctions de recherches ou la création d'index.</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-3s5.png" alt="ROTI 3/5" />

<h2 style="margin-top: 5em; clear: both;">[Conf] Recherche d’information (RI) : Fondements et illustration avec Apache Lucene</h2>

<p style="text-align: justify;">Comment marche un moteur de recherche d'information comme Lucene? C'est ce que nous propose de découvrir Majirus Fansi à travers un survol très clair et parfois en chanson des méthodes d'indexation.</p>

<p style="text-align: justify;">Cela commence par une présentation des différents termes et modèles utilisés pour la recherche d'information:</p>

<ul style="text-align: justify;">
    <li>modèle booléans</li>
    <li>modèle vectoriels: utilisé entre autre par Lucene</li>
    <li>modèle probabilistique (juste évoqué)</li>
</ul>

<p style="text-align: justify;">On enchaine ensuite sur les différentes étapes de construction d'un index inversé:</p>

<ul style="text-align: justify;">
    <li>décomposition du texte en mot</li>
    <li>suppression des mots vides de sens (<code>Stop word</code>) comme <code>de</code>, <code>la</code>, <code>les</code>, ...; on prendra garde à sélectionner ces <code>stop words</code> en prenant en compte le contexte du texte: <code>la</code> dans un texte musical n'est pas forcément vide de sens. On pourra remarquer que le plus souvent ces mots seront ceux qui sont le plus référencés par l'ensemble des textes, analyser le nombre d’occurrence peut donc être un moyen de les identifier.</li>
    <li>les mots sont ensuite transformés dans leur forme générique, lème ou terme: <code>chevaux</code> -&gt; <code>cheval</code>, <code>détruit</code> -&gt; <code>détruire</code> ... (c'est à ce stade que les moteurs intégrant de la recherche sémantique peuvent ramener le mot à ses catégories sémantiques: cheval, équestre, animal, mammifère...)</li>
    <li>l'index inversé est alors généré comme une <code>Multimap</code> java: le mot sert de clé et l'identifiant du document est ajouté à la liste des documents référençant ce mot.</li>
</ul>

<p style="text-align: justify;">Une petite illustration de tout cela, en prenant deux documents:</p>

<blockquote>Depuis sa création, Arolla s’est engagée résolument dans une démarche écologique.

Notre société est zéro carbone : chaque année, nous établissons un bilan précis de notre impact environnemental et le compensons en plantant des arbres... une charte écologique qui vous engage à des gestes quotidiens plus respectueux de l’environnement.</blockquote>

<p style="text-align: justify;">Index inversé:</p>

...

<table>
<thead>
<tr>
<th>Terme</th>
<th>Documents</th>
</tr>
</thead>
<tbody>
<tr>
<td>Arolla</td>
<td>1</td>
</tr>
<tr>
<td>engagement</td>
<td>1, 2</td>
</tr>
<tr>
<td>démarche</td>
<td>1</td>
</tr>
<tr>
<td>charte</td>
<td>2</td>
</tr>
<tr>
<td>écologie</td>
<td>1, 2</td>
</tr>
<tr>
<td>environnement</td>
<td>2 (2fois)</td>
</tr>
</tbody>
</table>

<p style="text-align: justify;">Nous voyons ensuite comment est calculé la pertinence d'un document pour une recherche: le <code>scoring</code>.
Une fois tous les documents récupérés grâce à l'index inversé, les logarithmes et formules mathématiques sortent de l'ombre... uh ! On retiendra qu'il s'agit d'un mélange de "plus un terme est présent dans les documents, moins il est informatif", les "mots recherchés apparaissant peu souvent auront un poids beaucoup plus important" dans le calcul de la pertinence: s'il apparaît peu globalement et en plus dans un seul document, alors celui-ci aura un niveau de pertinence plus important. Voila en gros l'idée, les logarithmes font ensuite le reste pour lisser et pondérer le tout.</p>

<p style="text-align: justify;">Enfin, nous voyons le modèle vectoriel et comment il intervient dans le calcul de la pertinence.
Considérons que l'ensemble des N termes de notre index inversé est un espace vectoriel de dimension N. Chaque document et notre recherche peux alors y être représenté comme un vecteur dont les coordonnées seront par exemple le nombre d’occurrence de chaque terme. Un peu confus tout ça? Une petite illustration alors !</p>

<p style="text-align: justify;">Voici notre espace vectoriel:</p>

<table>
<thead>
<tr>
<th>Terme</th>
<th>Dimension</th>
</tr>
</thead>
<tbody>
<tr>
<td>Arolla</td>
<td>1</td>
</tr>
<tr>
<td>engagement</td>
<td>2</td>
</tr>
<tr>
<td>démarche</td>
<td>3</td>
</tr>
<tr>
<td>charte</td>
<td>4</td>
</tr>
<tr>
<td>écologique</td>
<td>5</td>
</tr>
<tr>
<td>environnement</td>
<td>6</td>
</tr>
</tbody>
</table>

Dans cet espace, notre premier document aura les coordonnées suivantes:

<code>d1 = [1, 1, 1, 0, 1, 0]</code>

le second document aura alors les coordonnées suivantes:

<code>d2 = [0, 1, 0, 1, 1, 2]</code>

Une recherche avec <strong>engagement écologique et environnement</strong> aura les coordonnées suivantes:

<code>r = [0, 1, 0, 0, 1, 1]</code>

<p style="text-align: justify;">En considérant que chaque coordonnée est équivalente à un vecteur partant de l'origine, l'angle entre un document et le vecteur de recherche peux s'écrire: <code>cos(d1,r) = (d1.r) / (|d1||r|)</code> où <code>.</code> dénote le produit scalaire entre deux vecteurs et <code>|x|</code> correspondant à la norme du vecteur x, autrement dit sa longueur.</p>

<p style="text-align: justify;">Voir <strong><a href="http://lucene.apache.org/core/3_6_0/api/core/org/apache/lucene/search/Similarity.html">ici</a></strong> pour plus de détails sur les formules évoquées et utilisées par Lucene.</p>

<p style="text-align: justify;">Cette valeur comprise (en valeur absolue) entre 0 et 1 permet alors de pondérer la pertinence calculée précédement par "l'alignement du document avec la recherche". Si les deux vecteurs sont parfaitement alignés c'est à dire colinéaires, cette valeur sera égale à 1. Le poids ne sera donc pas diminué. En revanche, si les vecteurs ne sont pas alignés, cette valeur diminuera vers zéro, et la pertinence sera réduite.</p>

<p style="text-align: justify;">La suite de la présentation sera l'illustration de tout cela avec Lucene.</p>

<p style="text-align: justify;">En bref, une présentation très claire des techniques d'indexation. On regrettera la lecture mot à mot des transparents et les grosses équations qui n'apportent pas grand chose au propos sinon de faire décrocher un peu l'auditoire.</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" />

<h2 style="margin-top: 5em; clear: both;">Obésiciel et impact environnemental : Green Patterns appliqués à Java</h2>

<p style="text-align: justify;">Voila un sujet bien atypique dans une telle conférence, ma fibre verte vibre et je me lance :
Obésiciel et Green patterns par Olivier Philippot (<a href="http://twitter.com/#!/greencodelab">@greencodelab</a>).
Olivier Philippot commence par demander combien de personnes mesurent la consommation de leur machine chez eux... personnes, erf ! Après quelques constats sur la consommation de l'IT dans le monde (l'infrastructure de Facebook consomme autant qu'un TGV à pleine vitesse, les calculs nécessaires pour le film du Chat Potté ont consommé autant que la planète entière pendant 20min, NDR: oubliez la recursivité !).</p>

<p style="text-align: justify;">Comment se fait-il que sans rien faire son ordinateur me met à consommer de plus en plus, comment explique-t-on cette dérive: c'est l’obésiciel ou bloatware. Les logiciels installés consomment de plus en plus de ressources, laissent des processus résidents et consomment de l'espace disque, alors que l'on ne s'en sert probablement même plus. Ce à quoi on répond par des machines de plus en plus puissantes et des disques durs de plus en plus gros, et l'on consomme donc de plus en plus.</p>

<p style="text-align: justify;">Comment se fait-il qu'un onglet Chrome qui n'est pas affiché continue de consommer autant, pourquoi une application minimisée continue-t-elle de prendre des ressources... A-t-on besoin de prendre des Timers aussi rapides pour nos applications, ce qui ne permet pas au processeur de se mettre en veille: il ne fait que se réveiller et s'arrêter...</p>

<p style="text-align: justify;">Un très bon résumé chez <a href="http://blog.soat.fr/2012/04/devoxx-fr-2012-obesiciel-et-impact-environnemental-green-patterns-appliques-a-java/">Soat</a>.</p>

<p style="text-align: justify;">En bref, un sujet atypique, beaucoup de constats, mais peu de solutions concrètes, à suivre... Je vais me plonger dans le livre <a href="http://greencodelab.fr/Livre">Green Patterns - Manuel d'éco-conception des logiciels</a> afin d'approfondir un peu plus le sujet.</p>

<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-3s5.png" alt="ROTI 3/5" />

<h2 style="margin-top: 5em; clear: both;">.Net pour le développeur Java : une source d'inspiration ?</h2>

<a href="http://twitpic.com/9c0gcz"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/DotNetVsJava-thinkb4coding.jpeg" alt="" width="500px" /></a>

<p style="text-align: justify;">Un survol rapide de nombreux sucres syntaxiques et des possibilités de .Net par Cyrille Martraire (<a href="http://twitter.com/cyriux" target="_blank">@cyriux</a>) et Rui Carvalho (<a href="http://twitter.com/rhuy" target="_blank">@rhuy</a>):</p>

<ul style="text-align: justify;">
    <li>synthétique</li>
    <li>drôle</li>
    <li>instructif</li>
    <li>des transparents très esthétiques</li>
</ul>

<p style="text-align: justify;">On retiendra que même si Monsieur M. a initialement beaucoup copié Monsieur S., Monsieur M. a depuis bien avancé et même pris une sérieuse avance depuis la version 3 de C# en enrichissant énormément les possibilités offertes:</p>

<ul style="text-align: justify;">
    <li>définition rapide des accesseurs de propriétés (<code>get</code> et <code>set</code>)</li>
    <li>classes anonymes typées, qui permet de créer des structures intermédiaires fortement typées sans avoir à créer une classe dédiée juste pour faire passe plat</li>
    <li>closure</li>
    <li>intégration de Linq: permettant d'intégrer un langage de requêtage directement dans le code (comme du SQL) mais s'appliquant aussi bien à des sources de type bases de données qu'à des collections en mémoire d'objet.</li>
    <li>...</li>
</ul>

<div style="width: 100%; margin: 0pt auto; text-align: justify;">

<img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/DotNetVsJava.png" alt="Dot Net vs Java" />

<em><a href="http://speakerdeck.com/u/rhwy/p/net-pour-le-developpeur-java-une-source-dinspiration">Les slides</a></em>

</div>

<p style="text-align: justify;">Allez Java t'a du retard là !!    <img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-4s5.png" alt="ROTI 4/5" /></p>

<h2 style="margin-top: 5em; clear: both; text-align: justify;">CQRS</h2>

<p style="text-align: justify;">Et pour clore cet évènement: Mon coup de cœur ! CQRS (Command Query Request Segregation) par Jérémie Chassaing (<a href="http://twitter.com/#!/thinkb4coding">@thinkb4coding</a>)</p>

<p style="text-align: justify;">Une présentation et des explications très claires et fluides par un passionné sur un sujet qui mérite vraiment d'être creusé !</p>

<p style="text-align: justify;"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/CQRS-3Tiers.png" alt="CQRS vs 3Tiers" /></p>

<p style="text-align: justify;">La présentation commence par évoquer les architectures actuelles en empilements de couches (type lasagne), on l'on traverse successivement les couches: Controlleur -&gt; Service -&gt; DAO -&gt; ORM -&gt; base de données; puis l'on revient: base de données -&gt; ORM -&gt; DAO -&gt; Service -&gt; Controlleur. On se rend compte qu'à chaque couche, les données subissent des chaines de transformations uniquement pour passer de notre utilisateur à notre espace de persistance.
Que ce soit pour une simple consultation ou pour une mise à jour de données, la succession des couches est identique; alors qu'une mise à jour nécessite une intégrité fonctionnelle, une simple consultation de données passe par un requêtage complexe avec plusieurs jointures et projection afin d'adapter notre modèle transactionnel à un modèle de consultation. D'autant plus que généralement, 90% des requêtes utilisateurs seront des consultations pour seulement 10% de modifications (une observation générale qu'il convient d'ajuster selon le domaine métier).</p>

<p style="text-align: justify;">C'est de ce constat que l'architecture CQRS émerge: pourquoi ne pas séparer notre modèle d'écriture (utilisé pour les mises à jour: en écriture seule <code>Write Only</code>) de notre modèle de lecture (utilisé pour les consultations, en lecture seule <code>Read Only</code>): <strong>"Deux modèles pour deux besoins"!</strong>
Le modèle de lecture est mis à jour en réaction aux notifications envoyées par le modèle d'écriture lors de changements.</p>

<p style="text-align: justify;"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/CQRS-VuesDEnsemble.png" alt="CQRS: deux modèles" /></p>

<p style="text-align: justify;">Notre modèle d'écriture sera alors optimisé pour l'écriture et contiendra l'ensemble de nos règles métier. On <strong>pourra</strong> privilégier une approche de type DDD (Domain-Driven Design) afin de bien refléter les concepts métiers et de facilité la gestion des contours de transaction.
Notre modèle de lecture quant à lui sera optimisé pour la lecture en effectuant toutes les projections préalables et fournir directement les données requises. On pourra par exemple avoir directement une table dédiée à chaque page de notre application, les informations pourront être directement dénormalisées, plus de jointures complexes : se contenter alors d'un <code>select * from table</code> pour la récupération des données.</p>

<p style="text-align: justify;">Comme chaque modèle est séparé, il est possible d'adopter pour chacun des stratégies de stockage, de réplication et d'intégrités différentes. On pourra par exemple adresser la montée en charge différemment en optant pour du sharding pour les requêtes d'écriture et de la réplication/load balancing pour les requêtes de lecture.</p>

<p style="text-align: justify;">J'ai beaucoup aimé l'aspect non dogmatique et progressif de la mise en œuvre avec de nombreuses pistes évoquées:</p>

<ul style="text-align: justify;">
    <li>Partant du constat qu'il s'agit uniquement de décorréler modèle d'écriture et modèle de lecture, il est tout à fait possible de commencer simplement en créant une vue des tables existantes:
<code>create view list_users as select * from users</code>. Au fur et à mesure les besoins de lectures et d'écritures pourront alors diverger de manière transparente.</li>
    <li>Il est tout à fait possible d'adopter ce motif architectural à un sous-ensemble: il n'est pas requis que l'ensemble de l'application applique ce motif.</li>
    <li>La mise à jour du modèle de lecture peux très bien être synchrone au départ puis évoluer vers des approches de type "eventually consistent"</li>
    <li>...</li>
</ul>

<p style="text-align: justify;">C'est une présentation que je recommande de voir ou de revoir!</p>

<blockquote class="twitter-tweet" lang="fr">Présentation CQRS à DevoxxFr <a title="http://www.slideshare.net/jeremiechassaing/prsentation-cqrs-devoxxfr" href="http://t.co/Swrb0zuH">slideshare.net/jeremiechassai…</a> via @<a href="https://twitter.com/slideshare">slideshare</a>

— Jérémie Chassaing (@thinkb4coding) <a href="https://twitter.com/thinkb4coding/status/193684101658714113">Avril 21, 2012</a></blockquote>

<p style="text-align: justify;"><img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" /> <img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/AVoir-absolument.png" alt="A voir absolument!" /></p>

<h1 style="text-align: justify;"></h1>

<h1 style="text-align: justify;"></h1>

<h1 style="text-align: justify;"></h1>

<h1 style="text-align: justify;"></h1>

<h1 style="text-align: justify;"

&nbsp;
&nbsp;
&nbsp;

>Conclusion</h1>

<p style="text-align: justify;">Deux jours très intenses et franchement à revivre: très riches, très denses et très instructifs.</p>

<p style="text-align: justify;">Quelques mais: des pauses entre chaque conférence peut être trop courtes compte-tenu de certains changements de salles et la nécessité de faire des pauses techniques. L'accès des salles Seine A,B,C était difficile au milieu des stands partenaires.</p>

<p style="text-align: justify;"><img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/DevoxxFrance2012-200x14.png" alt="Devoxx France 2012" />
<img style="background: none; border: 0px; margin: 0px; padding: 0px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/ROTI-5s5.png" alt="ROTI 5/5" /></p>

<p style="text-align: justify;">On se retrouve pour Devoxx France 2013 !</p>
