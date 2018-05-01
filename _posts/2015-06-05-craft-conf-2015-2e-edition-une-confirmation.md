---
layout: post
title: "Craft Conf 2015 : 2e édition, une confirmation"
date: 2015-06-05 10:32
author: nicolas fedou
comments: true
categories: [Actu, architecture, craft conf, craftsmanship, Evénements]
---
Comme <a href="http://www.arolla.fr/blog/2014/05/craft-conf-a-budapest-le-rendez-vous-a-ne-pas-manquer/">l'an dernier</a>, la Craft Conf a réuni <a href="http://craft-conf.com/2015#speakers">des experts</a> sur ses différentes scènes. Elle était organisée par <a href="http://prezi.com/">Prezi</a>, <a href="http://www.ustream.tv/">Ustream</a> et <a href="http://www.ericsson.com/fr">Ericsson</a>. Grâce à Ustream, <a href="http://www.ustream.tv/craft">les vidéos sont en ligne en une semaine</a>.
Ces experts dans leur domaine nous ont généralement livré des retours d'expériences sur leur façon actuelle de travailler malgré quelques placements de produits (parfois mérités). Chaque année à la Craft Conf, soit on suit une présentation sur un sujet que l'on connaît et on y apprend des choses, soit on découvre la vraie profondeur des sujets que l'on avait juste effleurés jusque là.
Si vous construisez des systèmes logiciels, il y a sûrement des présentations qui peuvent vous plaire. Je vous laisse faire votre propre marché parmis <a href="http://craft-conf.com/2015#speakers">la liste des speakers (souvent connus) et le titre de leurs sujets</a>. Les abstracts sont cachés derrière un clic sur le "+". <a href="http://www.ustream.tv/craft">Les présentations sont sur Ustream</a>.

Si l'ambiance vous intéresse, le <a href="http://eventifier.com/event/craftconf15/">site Eventifier</a> s'était proposé de présenter les infos de médias sociaux par popularité et j'avoue que, même partiel, ce n'est pas si mal.

Pour vous donner une idée de ce que j'ai retiré de cette conférence, la suite de cet article va décrire <a href="http://fr.wikipedia.org/wiki/%C3%89tat_de_l%27art">l'état de l'art</a> du Craftsmanship en 2015 tel que les speakers me l'ont présenté.
En avertissement :

<ul>
<li>Cet état de l'art n'est donc pas complet (loin de là).</li>
<li>Il se base sur mes connaissances avant d'arriver à la Craft Conf.</li>
<li>Il présente surtout ce que j'ai appris grâce aux présentations que j'ai suivies (et pour les enquêteurs, oui, j'en regardé récemment sur Ustream).</li>
</ul>

<h1>L'état de l'art 2015 de la construction de système logiciels</h1>

On va parcourir cet état de l'art en zoomant du plus général, les méthodes, jusqu'au plus spécifique, les techniques.

<h2>De la vision fonctionnelle de l'entreprise</h2>

L'agile est souvent une affaire de développeurs, ce qui permet de livrer le minimum de logiciel pour répondre à un besoin. L'expression de besoin, par contre, est toujours mise au point dans un modèle de cycle en V...
Cette agilité limitée aux développeurs n'apporterait que 20 % des bénéfices d'une transformation agile dans l'entreprise entière. Les idées du marketing, des clients ou de n'importe qui devraient donner lieu à un essai le plus rapidement possible pour tester l'idée. Ainsi, les "MVP" ne doivent pas être des produits même minimaux, mais selon les cas : des "slides", des maquettes, des "landing pages", des prototypes, etc.
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/05/agileEnV.jpg"><img class="alignnone size-medium wp-image-3237" src="http://www.arolla.fr/blog/wp-content/uploads/2015/05/agileEnV-300x170.jpg" alt="agileEnV" width="300" height="170" /></a>
Il est inutile de faire des "roadmap", "business cases", spécifications fonctionnelles et détaillées, bref engager autant d'argent alors que l'on est incapable de prévoir le retour sur investissement réel d'une fonctionnalité. Autant faire en sorte d'une part, que l'on construise seulement les fonctionnalités qui ont une chance d'avoir un impact sur le business pour le moins cher possible et d'autre part que l'on apprenne des choses de ces fonctionnalités (leurs usages réels, leurs performances).
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/05/agileEnLean.jpg"><img class="alignnone size-medium wp-image-3236" src="http://www.arolla.fr/blog/wp-content/uploads/2015/05/agileEnLean-300x171.jpg" alt="agileEnLean" width="300" height="171" /></a>

On dit que notre métier évolue vite et qu'il faut toujours apprendre pour se tenir à jour. En effet, mais cela est vrai aussi pour le métier de nos logiciels ! Apprendre est bien une des activités principales de notre activité, mais aujourd'hui il faut apprendre de notre propre code en production.
En fait, le cycle en V nous a permis de savoir construire des logiciels. L'agile (bonnes pratiques craft - TDD, boucles de feedback avec le métier raccourcies) nous permet de savoir en construire moins et au plus près du besoin de nos clients (celui qui finance la réalisation du logiciel). Même si on doit continuer à découvrir comment mieux construire nos logiciels, l'apprentissage d'aujourd'hui est moins sur les frameworks et les méthodes.
Nous devons mesurer le succès de ce qu'on livre et apprendre de notre logiciel en production. La mesure des performances et de l'usage réel des features que nous livrons est importante, les mesures permettant de mieux connaitre nos utilisateurs aussi !

Si je résume en une citation :

<blockquote>
  Sustainably minimise lead time to business impact.
</blockquote>

Pour mettre en place une entreprise agile capable de mesurer l'efficacité et la pertinence des fonctionnalités qu'elle livre, une stratégie ne suffit pas. On parle de changements d'habitudes et de comportements, vous devez mettre en place une culture d'entreprise adaptée.
Voici un exemple de la part d'Atlassian :
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/06/atlassianDevOnCultureAndValues.jpg"><img class="alignnone size-medium wp-image-3238" src="http://www.arolla.fr/blog/wp-content/uploads/2015/06/atlassianDevOnCultureAndValues.jpg" alt="atlassianCulture" /></a>
Une fois l'organisation mise en place, il n'y a plus qu'à mettre les mains dans le code...

<h2>De la vision opérationnelle de l'entreprise</h2>

J'ai entendu de bons conseils sur le fait d'apprendre des logiciels que l'on met en production. Tout d'abord, si on veut apprendre quelque chose, il faut le rendre visible, donc le mesurer. L'usage des features et le suivi des utilisateurs sont donc des cas d'usages de premier plan. Ainsi, ceux qui surveillent la production et ceux qui surveillent les usages des utilisateurs sont eux-mêmes des utilisateurs du logiciel ! Ils méritent donc leurs "user stories".

Auparavant, pour maîtriser les impacts des évolutions d'un logiciel, j'entendais parler de modularité. Sur mes projets, j'ai souvent pu constater une érosion de l'architecture qui, finalement, liait les modules entre eux. Je vois les micro-services comme de la modularité poussée à l’extrême. Ce sont des livrables distincts, produits par plusieurs équipes qui ne peuvent plus accéder à l'implémentation d'un module voisin. Beaucoup de conférences parlaient ou faisaient référence aux micro-services. J'y ai appris deux, trois trucs.

Par exemple, la maxime qui dit que "tout système complexe est un petit système qui a grossi avec le temps" s'applique à la genèse d'une architecture en micro-services. Ainsi, on démarre toutes les applications sous forme de monolithe. Lorsque cette application devient trop grosse pour rentrer dans la tête d'un développeur, on en extrait une partie pour la mettre dans un nouveau service. Évidemment, une équipe se dédie à ce nouveau service : Loi de Conway oblige...

Un titre de conférence énonçait "Pourquoi une API est comparable à un chiot ?" Sauf échec, elle dure longtemps, jusqu'à la fermeture de l'entreprise. Elle a sa propre vie dans le sens où vos utilisateurs auront des besoins qui ne prennent pas du tout en compte votre joli modèle Rest/Hateoas. Elle se livre avec un mode d'emploi en plus de la documentation.
Le minimum est de livrer l'API ou le service avec une documentation pour avoir le vocabulaire et l'ensemble des appels : Javadoc ou Swagger. L'étape suivante est de fournir un stub pour que vos utilisateurs puissent écrire des tests. Ensuite, il s'agit de livrer un client mockable en plus du service documenté.

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/05/microSvcProvideSvcAndMockableClient.jpg"><img class="alignnone size-medium wp-image-3239" src="http://www.arolla.fr/blog/wp-content/uploads/2015/05/microSvcProvideSvcAndMockableClient.jpg" alt="microSvcWithMockableClient" width="600" height="327" /></a>

A propos de livrer de la documentation, Simon Brown et son modèle C4 ont bien progressé. L'an dernier, il proposait 4 schémas simples pour décrire un système logiciel. Cette année, il a présenté et mis en ligne un outil pour générer ces schémas à partir de votre code de production annoté d'une part et de quelques lignes de codes supplémentaires pour décrire l’extérieur et définir quelques labels.

Je ne résiste pas à l'envie de dire que le prochain livre de Michael Feather pour donner naissance à un concurrent de SonarQube. Oui, ce n'est qu'un Troll. Mais il avait présenté son travail sur l'analyse de données issues des historiques de commits... Il nous avait montré des patterns de classes sujettes au refactoring permanent et quelques autres indices devant attirer l'attention d'un responsable qualité.

J'ai encore quelques notes sous le coude comme la présentation de Sandro Mancuso qui présente un design objet en suivant la chronologie d'un appel utilisateur. L'appel arrive sur une couche applicative "chef d'orchestre" qui charge une entité du domaine pour qu'elle applique la commande utilisateur, etc... Bref, une variante de DDD qui commence par de l'objet et non par de l'architecture. Cette approche est le pendant design de son approche TDD "outside-in".

<h1>Et il y avait aussi plein d'autres sujets...</h1>

Mais l'article est bien assez long et j'espère vous avoir aider à savoir ce qui se passe à la Craft Conf de Budapest.
En plus d'Eventifier, il y a des gens qui ont publié des articles :

<a href="http://theburningmonk.com/tags/craftconf/">The Burning Monk nous livre des articles détaillés</a> sur les conférences les plus inspirantes.

<a href="http://idisposable.co.uk/2015/04/craftconf-2015-did-someone-say-microservices/">idisposable.co.uk - première participation</a>

Un retour d'un participant ayant fait les deux années :
* <a href="https://survivalofthecraziest.wordpress.com/2015/04/25/craft-conf-2015-day-2/">Survival of the Craziest - Kartik Sachdev - Craft Conf 2015, Day 2</a>
* <a href="https://survivalofthecraziest.wordpress.com/2015/04/25/craft-conf-2015-day-3/">Survival of the Craziest - Kartik Sachdev - Craft Conf 2015, Day 3</a>

<a href="http://michal.paluchowski.com/the-state-of-the-craft-at-craftconf-2015/">L'état de l'art à Craft Conf (en anglais)</a>

Et beaucoup, beaucoup d'autres ...
