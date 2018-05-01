---
layout: post
title: Les numéros de version vont-ils disparaître ?
date: 2013-07-15 11:30
author: christophe michel
comments: true
categories: [beta, Bonnes pratiques de dév, branch, build, chrome, Continous Delivery, feature branch, feature toggle, GIT, Programmation, release, source, svn, version]
---
<p dir="ltr" id="docs-internal-guid-65cb81d7-d301-78e2-3c6a-58a90a7ea416" style="text-align: justify;">Quand on parle d’espèces menacées, on pense facilement au guépard ou au thon rouge. Mais je suis prêt à parier que vous ne pensez pas aux numéros de version.</p>

<p style="text-align: justify;">Et pourtant, silencieusement, ces monuments de l’industrie logicielle sont en train de disparaître du paysage. Et cette évolution a des choses à nous apprendre.</p>

<h2 id="docs-internal-guid-65cb81d7-d301-4b62-ddaa-0cbc81a96b9f" style="text-align: justify;">Le numéro de version, sa vie, son œuvre</h2>

<p dir="ltr" style="text-align: justify;">Dans leur définition la plus large possible, les numéros de version sont omniprésents dans le monde logiciel. Au plus bas niveau on trouve des numéros de build ou des révisions de VCS, on a ensuite des numéros de version privés (alpha, beta et autres releases internes) jusqu’aux numéros de version publics.</p>

<p dir="ltr" style="text-align: justify;">Nous les utilisons fondamentalement pour la même chose : mettre un nom sur un ensemble de changements ou de fonctionnalités, ce qui est indispensable pour communiquer, que ce soit entre développeurs, dans des relations client/support ou des contrats de licence.</p>

<p dir="ltr" style="text-align: justify;">Si le concept de numéro de révision a disparu dans les systèmes de gestion de version distribués, il y a toujours des étiquettes associées aux modifications (e.g. le hashtag d’un commit dans git) parce que nous développeurs en avons besoin pour travailler au quotidien.</p>

<p style="text-align: justify;">Qu’en est-il d’un numéro de version public, celui qui identifie votre logiciel auprès des clients et utilisateurs ? C’est ce que nous allons voir...</p>

<h2 id="docs-internal-guid-51d958a2-d302-dabb-c92b-8f8dfe350454" style="text-align: justify;">Au commencement</h2>

<p style="text-align: justify;">Avec la dématérialisation du logiciel, on tend à oublier qu’à une certaine époque, livrer une version était un processus long et coûteux. Les moins de 20 ans ne connaîtront jamais l’expérience unique d’installer une version d’Office occupant 36 disquettes...</p>

[caption id="" align="aligncenter" width="500"]<a href="https://secure.flickr.com/photos/osmium/337923676/"></a> Crédit photo : Josh Gallaway (CC BY-NC-SA 2.0)[/caption]

<p dir="ltr" id="docs-internal-guid-65cb81d7-d306-68b5-3c68-ebb8298f941c" style="text-align: justify;">Les numéros de version sont alors associés à une réalité matérielle qui contraint le travail des développeurs et force à regrouper les changements par des lots bien définis et de taille suffisante pour justifier la sortie d’une nouvelle version.</p>

<p dir="ltr" style="text-align: justify;">A mesure que la distribution du logiciel s’est simplifiée, il est resté d’autres contraintes techniques : validation fonctionnelle, tests de charge, procédure de déploiement ou de mise à niveau...</p>

<p style="text-align: justify;">Par ailleurs, au niveau commercial on trouve l’accord de licence. Les versions sont alors un moyen de vendre un service particulier, l’utilisateur devant repayer pour obtenir la version suivante et les nouvelles fonctionnalités qu’elle apporte.</p>

<h2 id="docs-internal-guid-470b3326-d306-0bb3-cb08-a903461be42f" style="text-align: justify;">wwwho needs version numbers anyway ?</h2>

<p dir="ltr" style="text-align: justify;">L’apparition des applications web a fait exploser la contrainte de distribution. Dans un modèle devenu centralisé, il est malaisé de maintenir plusieurs versions en parallèle, versions qui doivent être hébergées séparément. Il est bien plus simple d’offrir la même pour tous les utilisateurs.</p>

<p dir="ltr" style="text-align: justify;">Les modèles de licences évoluent du paiement à l’achat (adapté à un logiciel considéré comme un produit vendu sur un support) vers le paiement à l’utilisation (adapté à un logiciel considéré comme un service).</p>

<p style="text-align: justify;">Un certain nombre d’application web ont donc simplement abandonné le concept de numéro de version public. Vous ne savez pas quelle version de Gmail vous utilisez et il est probable que vous ne savez pas non plus avec quelle version de l’application web de votre banque vous passez vos virements.</p>

<h2 id="docs-internal-guid-5c7ecd70-d307-9dc2-4e50-d621afff96a3" style="text-align: justify;">Le cas Google Chrome</h2>

<p dir="ltr" style="text-align: justify;">Google a poussé l’expérience plus loin. Profitant du fait que les utilisateurs de son navigateur Chrome étaient par définition connectés à Internet, ce dernier se met à jour automatiquement, silencieusement et le numéro de version n’est pas public.</p>

<p dir="ltr" style="text-align: justify;">Cette modification a fait des vagues, de nombreux utilisateurs ayant le sentiment de perdre le contrôle du logiciel, d’autant plus que traditionnellement le développement web s’effectuait vis à vis de versions particulières de chaque navigateur, du fait d’importantes différences dans le support des standards.</p>

<p dir="ltr" style="text-align: justify;">Le lead developer de Google Chrome, Ben Goodger, <a href="https://plus.google.com/105636695715347097518/posts/G9hbCEMC2wF">s’en est expliqué ainsi</a> :</p>

<blockquote>
<p dir="ltr">The Chrome autoupdater works quietly in the background, never notifying you. If there's an update, it'll download it and prepare it so that the next time you start the browser it's the latest version. <strong>Sort of like how the next time you load GMail it's the latest version.</strong></p>
</blockquote>

<p dir="ltr" style="text-align: justify;">Depuis, le navigateur concurrent Firefox a lui-aussi adopté un système similaire (cependant plus visible) même si les numéros de versions publics ont été conservés.</p>

<p dir="ltr" style="text-align: justify;">Le raisonnement à l’origine de ce choix de design est assez simple : aussi bien pour des raisons de sécurité que de fonctionnalités, il est hautement désirable que les utilisateurs disposent toujours de la toute dernière version. Dès lors, est-il vraiment pertinent de leur laisser le choix d’utiliser une version particulière ? Et une fois acté qu’on ne leur laissait pas ce choix, pourquoi rendre visible le processus de mise à jour ?</p>

<p dir="ltr" style="text-align: justify;">L’autre point intéressant soulevé par Ben Goodger est le suivant et c’est là que je voulais en venir :</p>

<blockquote>Autoupdate is one of Chrome's killer features. It is magical because it continuously updates an entire development platform invisibly, frequently. <strong>Supporting it has driven how we structure our development processes.</strong></blockquote>

<h2 id="docs-internal-guid-6b59b14f-d307-1df4-a6dc-c04a4054d087" style="text-align: justify;">Impact sur le développement</h2>

<p dir="ltr" style="text-align: justify;">Les politiques de versions sont toujours intimement liées à la façon de développement, dans un sens ou dans l’autre.</p>

<p dir="ltr" style="text-align: justify;">Pour Chrome, il s'agissait d'un cas assez extrême où la construction entière de l'application s'est faite sur le postulat que l'évolution du logiciel serait continue. Mais ce type de choix n'est pas toujours possible.</p>

<p dir="ltr" style="text-align: justify;">Lorsque la politique de versions n’est pas librement choisie, elle découle souvent du coût et de la vélocité de la mise en production. Plus il est “douloureux” de mettre en production, plus les versions seront espacées et rigides.</p>

<p dir="ltr" style="text-align: justify;">Un tel fonctionnement impose de recourir à des features branches (ou des artifices comme le shelve de Subversion ou le stash de git) et conduit à des merges souvent d’autant plus coûteux que les développements longs divergent du trunk.</p>

<p dir="ltr" style="text-align: justify;">Ces problèmes ne sont pas nouveaux et ont conduit à la création d’outils et de méthodes pour rendre la mise en production plus simple. Intégration continue, “Feature Flipping” et outils de migration de schéma de bases de données permettent de rendre la mise en production rapide, simple et fiable.</p>

<p style="text-align: justify;">On peut prendre comme exemple les pratiques de déploiement du site américain Etsy, dont le directeur technique Mike Brittain a fait une présentation en janvier dernier intitulée “<a href="http://www.slideshare.net/mikebrittain/mbrittain-continuous-deploymentalm3public">Continuous Deployment : the Dirty Details</a>” :</p>

[caption id="" align="aligncenter" width="500"]<a href="http://fr.slideshare.net/mikebrittain/mbrittain-continuous-deploymentalm3public"><img alt="" src="http://i.imgur.com/t5KSTvX.jpg?1" width="500" height="281" /></a> Etsy déploie jusqu'à près de 80 fois par jour en production[/caption]

<p dir="ltr" id="docs-internal-guid-65cb81d7-d308-d0c1-83e1-207bc6072191" style="text-align: justify;">Dans un tel contexte, tout le monde travaille sur une seule et unique branche et on déploie en continu des fonctionnalités non terminées mais pas forcément activées ou visibles.</p>

<p dir="ltr" style="text-align: justify;">On arrive donc à un point où, s’il fallait marquer d’une version chaque changement du logiciel, il y aurait de l’ordre de 15000 versions par an pour Etsy ! La notion de version publique perd donc ici tout son sens.</p>

<h2 style="text-align: justify;">Et après ?</h2>

<p dir="ltr" style="text-align: justify;">La généralisation des applications web et du SaaS, associés à des pratiques de développement qui tendent de plus en plus vers le déploiement en continu vont réduire à terme les numéros de version à leur portion congrue.</p>

<p dir="ltr" style="text-align: justify;">Ils resteront utiles voire indispensables partout où il y a un besoin impératif d’être d’accord sur un ensemble de fonctionnalité précis, comme par exemple pour des APIs ou des composants.</p>

<p style="text-align: justify;">Mais il est clair que pour l’utilisateur final, le numéro de version ne sera bientôt plus qu’une relique du passé informatique.</p>

<p style="text-align: justify;">Et vous, quelle est la politique de version pour votre produit ?</p>

<address><i>Christophe est consultant Arolla, avec un goût particulier à la fois pour le développement web coté serveur en Java et coté client en HTML5, le tout en TDD. Il est aussi difficile à battre à la pétanque. Retrouvez Christophe sur Twitter : </i><i><a href="https://twitter.com/christopheml" target="_blank">@christopheml</a> et le blog Arolla sur <a href="https://twitter.com/arollafr" target="_blank">@arollafr</a></i></address>
