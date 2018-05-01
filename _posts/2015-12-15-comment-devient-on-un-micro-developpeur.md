---
layout: post
title: Comment devient-on un micro-développeur ?
date: 2015-12-15 15:51
author: yann danot
comments: true
categories: [architecture, Bonnes pratiques de dév, développement, microservices, Programmation, software craftsmanship]
---
<p style="text-align: justify;">Pour être un bon micro-développeur, vous allez devoir vous familiariser avec certaines notions. La principale difficulté réside dans les communications interservices. Voici quelques recommandations pour mener à bien votre mission:</p>

<ul style="text-align: justify;">
    <li><strong>Eviter à tout prix les intégrations par base de données</strong>. Si plusieurs microservices communiquent avec la même base de données, vous n’aurez plus du tout d’indépendance. Chaque modification de schéma impliquera des modifications de tous les µservices y étant liés.</li>
    <li>Privilégiez les appels <strong>REST </strong>sur les mécanismes de <strong>RPC </strong>(Remote Procedure Call). Les intégrations de services via <strong>REST </strong>sur <strong>HTTP </strong>sont un bon choix pour débuter.</li>
    <li>N’exposez pas les détails de vos implémentations dans vos APIs, gardez vos APIs technologiquement agnostiques. Il faut éviter les technologies d’intégration qui imposent la technologie ou le langage à utiliser.</li>
    <li>Un microservice doit être simple d’utilisation.</li>
    <li>Préférez toujours la <strong>chorégraphie </strong>à <strong>l’orchestration </strong>de service. Prenons un exemple simple : lors d’une création de compte sur mon site de e-commerce, je souhaite créditer des points de fidélité au client, lui envoyer un cadeau par la poste et lui envoyer un mail de bienvenue.</li>
</ul>

<p style="text-align: justify;"><strong>L’orchestration </strong>consisterait en :</p>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/graph1.png"><img class="alignnone size-full wp-image-3600" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/graph1.png" alt="graph1" width="489" height="232" /></a>

La <strong>chorégraphie </strong>en :

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph2.png"><img class="alignnone size-full wp-image-3601" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph2.png" alt="Graph2" width="667" height="263" /></a>

&nbsp;

<ul>
    <li style="text-align: justify;">Optez pour une collaboration asynchrone par évènement. Les différents brokers de message comme Apache QPid, OpenAMQ, <a href="http://www.windowsazure.com/en-us/develop/net/how-to-guides/service-bus-amqp-overview/">Windows Azure Service Bus</a> ou encore RabbitMQ vont vous permettre de publier et consommer des évènements assez simplement. Cependant il faut savoir qu’il y aura un coût supplémentaire pour la mise en place et la maintenance d’une telle solution.</li>
    <li style="text-align: justify;">Publiez tous vos messages sur un seul bus, lisible par tout le monde.</li>
</ul>

<ul style="text-align: justify;">
    <li>Il faut intégrer dans votre travail le fait que chaque appel à un microservice peut échouer. Il va donc falloir être beaucoup plus attentif à la gestion d’erreur.</li>
    <li>Privilégiez toujours <strong>l’autonomie </strong>sur <strong>l’autorité</strong>.</li>
</ul>

<p style="text-align: justify;">Un service autonome doit pouvoir assurer sa responsabilité seul. Dans le cas d’une récupération de donnée une configuration en <strong>autonomie </strong>pourrait être:</p>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph3.png"><img class="alignnone size-full wp-image-3602" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph3.png" alt="Graph3" width="683" height="232" /></a>

Alors qu’une configuration <strong>autoritaire </strong>serait :

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph-4.png"><img class="alignnone size-full wp-image-3603" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Graph-4.png" alt="Graph 4" width="682" height="237" /></a>

<p style="text-align: justify;">Lorsqu’un service a l’autorité sur un autre, les données sont toujours à jour, alors que lorsqu’il est autonome, il existe une latence. Cependant dans un laps de temps plus ou moins court, les données seront mises à jour. Notez tout de même que pour rester autonome la <strong>gestion par évènement</strong> est parfaite.</p>

<ul style="text-align: justify;">
    <li>Prenez en compte que les données ne sont pas tout le temps à jour dans des systèmes distribués, on parle de “<strong>eventual consistency</strong>”.</li>
    <li>Acceptez la <strong>panne</strong> de vos services, mais prévenez haut et fort tous leurs utilisateurs.</li>
</ul>

<p style="text-align: justify;">Les développeurs de Netfilx sont partis du principe que la panne surviendra forcément. Ils ont créé un outil la provocant volontairement pour tester en prod la <strong>résilience</strong> de leur système, le Chaos Monkey. Ils ont également mis à disposition une librairie très utile pour gérer les interactions dans un système distribué : Hystrix.</p>

<ul style="text-align: justify;">
    <li>Pensez à des mécanismes de compensation pour contrer les problèmes d’eventual consistency.</li>
    <li>Supervisez. Le monitoring va devenir beaucoup plus complexe dans une architecture distribuée.
<ul>
    <li>Monitorez les temps de réponse utilisateurs. Le RUM (Real User Monitoring) est un outil idéal, il existe des solutions clés en main sur le marché comme <em>New Relic,</em> <em>mPulse de SOASTA</em> ou encore <em>pingdom</em>.</li>
    <li>Utilisez des Time Series Database qui permettent de stocker des métriques à intervalle régulier, comme <em>InfluxDB</em> (rétrocompatible avec <em>Graphite</em>) ou <em>OpenTSDB</em>.</li>
    <li>Traquez toutes les communications, Hystrix fournit ce service nativement.</li>
    <li>Standardisez vos logs et trouvez un moyen de les agréger.</li>
    <li>Utilisez des identifiants pour les différentes requêtes (corrélation ids).</li>
</ul>
</li>
    <li>Formez des équipes pluridisciplinaires pour être autonome.</li>
    <li>Découpez finement mais intelligemment vos services. Les microservices doivent pouvoir être réécrits intégralement en peu de temps. De ce fait, ils doivent être de très petite taille et compréhensibles rapidement.</li>
    <li>Renseignez-vous sur l’offre du marché. Il existe plusieurs outils qui vous aideront, en voici une liste, non exhaustive bien sûr :
<ul>
    <li><strong>Vert.x</strong> : Boîte à outil polyglotte facilitant l’implémentation d’application réactive.  Utilisé notamment par <em>RedHat, Bosch, Cyanogen, Groupon</em>…</li>
    <li><strong>Hystrix : </strong>Librairie (portée dans plusieurs langages) développée par les ingénieurs de Netflix permettant d’être tolérant à la panne facilement et notamment d’éviter les effets domino.</li>
    <li><strong>Docker</strong> : docker va permettre de conteneuriser vos microservices, de les tester et déployer plus facilement</li>
    <li><strong>Apache Kafka </strong>vous aidera si vous partez sur une solution event-sourcing</li>
    <li><strong>Apache Flume </strong> vous permettra de collecter et agréger les logs de vos différents services</li>
    <li><strong>Microsoft Azure Service Fabric</strong> est une solution complète pour orchestrer des services distribués.</li>
</ul>
</li>
</ul>

<p style="text-align: justify;">Ces différentes recommandations vous serviront certainement à la mise en place d’une architecture en microservice. Mais vous l’aurez compris, ceci est une philosophie à adopter et non pas une suite d’outils à utiliser. Ne tombez pas dans le piège consistant à vouloir profiter à tout prix de tous les avantages d’une telle architecture. Restez pragmatique en vous focalisant sur les points qui répondent à vos problématiques.</p>
