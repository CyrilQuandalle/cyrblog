---
layout: post
title: Les microservices, le régime tendance
date: 2015-08-05 10:31
author: yann danot
comments: true
categories: [architecture, Bonnes pratiques de dév, développement, microservice, Programmation, software craftsmanship]
---
<p style="text-align: justify;">Aujourd'hui, nombreuses sont les entreprises qui ne seraient rien sans leur SI. Depuis des années, l'informatique est au centre de nos activités professionnelles. C'est pourquoi, mes chers contributeurs, depuis tout ce temps vous me chérissez, MOI, votre application. Je suis votre bébé et vous m'élevez de la meilleure des façons. Pour mon développement, vous êtes très organisés et êtes à mon écoute. Vos méthodes d'éducation sont agiles et vous prenez bien soin de moi en me nourrissant régulièrement. Vous m'entretenez bien et me nettoyez souvent.</p>

<h2 style="text-align: justify;">La complainte d'une application monolithique</h2>

<p style="text-align: justify;">Force est de constater que j'ai bien grandi ! Et je dois vous avouer que j'ai mal à chaque nouveau projet. Certains de mes composants sont trop vieux et trop enfouis pour que vous puissiez faire quoi que ce soit. Je commence à douter de moi et j'ai du mal à me débrouiller en production. Je me sens lourde, lente et je ne m'aime plus. Cela fait des années que je m'engraisse et cela commence à se voir. Je vous entends parler de moi comme d'un monstre et certains d'entre vous pensent même à démissionner pour ne plus me voir, tellement il est devenu difficile de me moderniser. Tout s'emmêle en moi et mes entrailles ressemblent à un plat de spaghetti. Mes chers fondateurs, ça ne va plus, je suis devenue un immonde <strong>monolithe</strong>.
Mes copines, réactives, résilientes et scalables (que je jalouse !) m'ont parlé d'un régime très efficace : les <strong>microservices</strong>. Je ne vais ni vous parler d'un nouveau concept, ni d'un nouveau framework à la mode. L'approche microservices n'est pas vraiment innovante dans ses principes et ses racines remontent à loin. On peut par exemple voir que l'architecture Linux a été conçue avec cette approche. Cependant il me semble que la mise en place de microservices va vous guider de façon naturelle dans le respect des principes de SOLID, particulièrement sur le côté SRP, mais je vais y revenir plus tard.</p>

<h2 style="text-align: justify;">Un régime payant</h2>

<p style="text-align: justify;">Pour faire bref, l'architecture en microservices est une approche pour développer une application en un ensemble de petits services, chacun s'exécutant dans son propre processus et communiquant avec les autres via des mécanismes légers (REST, AMQP etc...). Ces services doivent être caractérisés par un besoin business ou technique (envoi de notifications par ex.), et déployables de façon indépendante et un minimum automatisée. Il sera possible de les écrire en différents langages adaptés à chaque besoin. De même les services les plus sollicités pourront être «scalés» de manière plus aisée. Les ressources seront allouées spécifiquement pour ce qui est indispensable au bon fonctionnement du système. Mais laissez-moi vous expliciter plus en détails pourquoi l'approche microservices fera de moi une bien meilleure collaboratrice.</p>

<h6 style="text-align: justify;">Des temps de développement diminués</h6>

<p style="text-align: justify;">La raison qui m'amène à penser que cette architecture serait meilleure est principalement l'indépendance des éléments. En effet, aujourd'hui, chaque modification de votre part amène une compilation de tous mes organes, et le chemin pour la voir arriver en production est long et fastidieux. Je suis un bloc indivisible alors que si j'étais décomposée en services, un changement quelque part n'entraînerait que la compilation et le déploiement d'un seul de mes membres. Plus un service serait unitaire, plus le temps perdu à compiler et à déployer serait court.
Cependant, certains développements entraîneraient des modifications d'interface et vous demanderaient un peu de coordination. Il existe néanmoins des mécanismes permettant de minimiser ces effets, la communication par "messaging" par exemple, mais un bon découpage devrait atténuer la cohésion et les liens entre services. Tout changement d'une interface devrait vous faire réagir, et devrait vous amener à vous poser des questions et peut-être revoir mon découpage. Les évolutions seront plus simples à suivre qu'actuellement.</p>

<h6 style="text-align: justify;">Des responsabilités isolées</h6>

<p style="text-align: justify;">Chaque service doit avoir une responsabilité identifiée, ceci réduira la complexité et améliorera la vision du métier de l'entreprise. Cet aspect est essentiel au bon déroulement d'une mise en place de microservices, si un composant correspond à plusieurs domaines vous allez vous compliquer la tâche et ne pas voir de réels gains. Le respect du principe SRP est fondamental. Il faut être le plus granulaire possible sans pour autant tomber dans l'obsession de créer un nouveau service pour tout et n'importe quoi. Toute la difficulté résidera dans le découpage, comment trouver le juste milieu. Cette question reviendra sans cesse et il n'existe malheureusement pas de recette miracle. Nous verrons dans un article suivant quels sont les défis qui vous attendent lors d'une refonte en microservices, et nous verrons qu'il faut commencer par les briques fonctionnelles facilement identifiables et expliciter le métier du monolithe pour en extraire des microservices.</p>

<h6 style="text-align: justify;">Une fluidité accrue au quotidien</h6>

<p style="text-align: justify;">Comme nous l'avons vu précédemment, un service s'exécute dans un processus indépendant, du coup tout changement dans le code d'un service implique le déploiement de celui-ci uniquement. Chaque service a son propre cycle de vie et ceci entraînera une meilleure fluidité dans vos réalisations. Grâce à cette isolation, un problème (retard des développements, changement fonctionnel etc.) lors de l'implémentation d'un projet n'entraînera pas le blocage de la réalisation d'une fonctionnalité différente. Si vous isolez en plus de la couche applicative la couche data, les données relatives à un service pourront être mises à jour, migrées ou traitées sans craindre d'effets de bord. Chaque service va également avoir sa propre durée de vie, il sera plus pratique de supprimer une fonctionnalité si celle-ci est isolée et n'interagit que très peu avec le reste de l'application. En effet, si les communications sont faites de façon assez souple (messaging, push d'event etc...), il suffira de le débrancher.
La mise en place d'A/B Test sera fluidifiée, pour comprendre le comportement de mes utilisateurs vous pourrez essayer plus de choses. Il suffira de déployer plusieurs versions différentes d'un service et d'ajuster au fil de l'eau. En y réfléchissant bien, la composition en microservices vous permettra d'être beaucoup plus agile et vous pourrez collecter le feedback de nos clients plus rapidement.</p>

<h6 style="text-align: justify;">La scalabilité facilitée</h6>

<p style="text-align: justify;">Il est assez facile d'imaginer le gain d'une architecture en microservices pour la scalabilité d'une application. Aujourd'hui, la production est conçue de manière horizontale, pour augmenter mes capacités, vous me divisez en plusieurs instances et me faites tourner sur plusieurs serveurs derrière un load-balancer. Les microservices étant des processus isolés, l'augmentation ou la diminution des ressources allouées par service pourra se faire de manière indépendante. Aujourd'hui, vous ne vous en apercevez peut-être pas mais mes performances sont médiocres et ceci est dû à un petit nombre de classes qui, à mon sens, devraient être isolées et scalées pour éviter l'effet bottleneck. La scalabilité d'un monolithe est plus compliquée car scaler un service c'est scaler l'ensemble. Certains composants sont plus complexes que d'autres et peuvent rendre impossible la scalabilité de l'ensemble. C'est le principal problème qui m'a fait prendre conscience du bien-fondé d'une structure en microservices.</p>

<h6 style="text-align: justify;">Un métier mieux maîtrisé</h6>

<p style="text-align: justify;">Plus besoin de chercher pendant des heures pour trouver les informations relatives à une fonctionnalité, la taille limitée et l'isolation des services vous permettra d'être plus précis dans vos recherches et de limiter le scope puisque toutes les informations liées à un domaine seront englobées dans un composant indépendant.</p>

<h6 style="text-align: justify;">Possibilité d'externalisation</h6>

<p style="text-align: justify;">Aujourd'hui notre société est "au top", vous avez développé des fonctionnalités géniales qui, à mon sens, pourraient être réutilisées par d'autres. Vous avez une expertise dans un domaine clé et je pense que cette approche pourrait vous apporter une source de revenus supplémentaire si vous décidiez de vendre l'utilisation d'un service en particulier.</p>

<h6 style="text-align: justify;">Un futur prometteur</h6>

<p style="text-align: justify;">L'innovation se fera sans risque, un composant à changer ? Même pas peur ! Du fait de leur taille raisonnée, les microservices sont plus maintenables. La mise à jour des différents frameworks par exemple sera beaucoup moins douloureuse car les impacts seront bien mieux maîtrisés. Finis les chantiers de six mois pour combler une faille de sécurité dont vous n'êtes même pas responsables. Vous pourrez explorer de nouvelles pistes technologiques sans remettre en cause l'existant, l'échec sera enfin permis et vos idées pourront voir le jour.</p>

<h2 style="text-align: justify;"></h2>

<h2 style="text-align: justify;"></h2>

<p style="text-align: justify;">Voilà mes chers collaborateurs, je vous ai présenté une possibilité de me rendre heureuse. Je pense sincèrement qu'une telle refonte est nécessaire tant je suis devenue lourde. J'ai besoin de ça pour continuer à vivre de manière décente. Les avantages explicités ne sont pas exhaustifs et je pense que vous en découvrirez d'autres au fur et à mesure de mon régime. Certains détracteurs diront (à juste titre), qu'il n'y a rien de nouveau dans cette façon de voir, que je suis déjà quasiment construite de la sorte. En effet ma structure interne est plutôt bien pensée, mais l'architecture en microservices nous donnerait un coup de pouce pour toujours mieux isoler les responsabilités de chacun de mes composants. Machiavel nous l'a pourtant dit, il faut «diviser pour mieux régner», alors je vous en prie écoutez-moi.</p>

<p style="text-align: justify;"><strong>PS</strong> : Cette introduction s'inscrit dans une suite de 3 articles, les 2 prochains arriveront bientôt. Nous parlerons des «enjeux d'une mise en place» puis nous verrons «un exemple concret sur un site e-commerce»</p>
