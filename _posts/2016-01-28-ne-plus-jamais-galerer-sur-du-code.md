---
layout: post
title: Ne plus jamais galérer sur du code !
date: 2016-01-28 18:07
author: nicolas fedou
comments: true
categories: [Architecture Hexagonale, bdd, Bonnes pratiques de dév, clean code, craftsmanship, DDD, devops, infrastructure as code, living documentation, Pair Programming, POO, Programmation, Refactoring Legacy Code, SOLID, TDD]
---
Sur toutes les applications que j'ai croisées chez des éditeurs de logiciels comme dans des DSI, nous avons trop souvent les mêmes bases de codes.

En Java ou en dotNet, il y a une "architecture en couches techniques" qui fournit un miroir de la base de données relationnelle. SQL permet d'attaquer le modèle sous tous les angles. Les ORM sont moins souples mais remontent cette ouverture d'esprit dans le code et le vocabulaire technique de notre modélisation objet relationnel se diffuse dans toute notre base de code... Chaque bean, EJB ou "service" contient une procédure (code métier ne contenant que du vocabulaire technique) qui aura un usage unique d'entités anémiques (POJO, objets ne contenant que des champs et des accesseurs). Les tests, quand ils existent, sont écrits après le code pour ne faire passer qu'un cas nominal ou deux. Les impacts sont nombreux et douloureux...

Vous cherchez des solutions testées par d'autres, à être épaulé par une communauté de professionnels cherchant ce même but? Cette communauté existe, c'est le mouvement Software Craftsmanship.

En voici quelques exemples :

<h2>Clean Code</h2>

Nos clients, BA, PO et autres porteurs d'un besoin ne parlent pas d'un état en trigramme concaténé par des "underscore". Ils parlent d'un comportement avec le vocabulaire de l'utilisateur final. Il y a du coup un fossé entre ce qui est dit par les porteurs de nos spécifications (ou user stories) et ce qui est exécuté par notre code. C'est un code illisible que l'on doit analyser pour réellement le comprendre et le faire évoluer. Cette compréhension était là lors de la première écriture du code et on doit la reconstruire à chaque relecture... Nous passons 80% de notre temps à lire du code et un code écrit une fois est relu une centaine de fois.

Vous connaissez Clean Code et la Living Documentation ? Le slogan du livre "Clean Code" est "make it work, make it clean, make it fast". Cela signifie qu'une fois le code opérationnel, nous devons tout de suite le revoir pour en faciliter la lecture. Tous les commentaires pouvant décrire l'utilité du code ou son intention devraient être exprimés sous forme de code. Cela peut être des noms de variables, des blocs de code exportés dans des méthode privées et bien nommées. Le livre Living Documentation va généraliser ce principe sur l'ensemble de votre produit logiciel. (Oui, je donne une vue d'ensemble rapide, donc très simplifiée.)

<h2>DDD, Architecture Hexagonale</h2>

Une User Story parle de comportement, donc d'une transformation, pas d'un état seul. Le modèle de la base est une conséquence de nos besoins métier, un détail d'implémentation. Non seulement nos analystes attendent un comportement de la part de nos logiciels, mais dès la mise en production, ce que nous devons corriger et faire évoluer, ce sont bel et bien ces comportements. La première chose à définir dans un logiciel, c'est le code métier qui, d'ailleurs, ne devrait pas dépendre de la base de données ni de quoi que ce soit.

Vous connaissez DDD et l'architecture Hexagonale ? Ces deux choses sont bien plus vastes que le problème "data centric" versus "behavior centric" que je décris ci-dessus. Ce qui m'intéresse ici, c'est le fait que l'on doive écrire un code métier AVANT son socle technique pour que le code métier impose ses contraintes au socle et non l'inverse. On verra très vite qu'il vaut mieux écrire les tests métier avant le code métier... Patientez, TDD arrive !

<h2>POO, SOLID</h2>

En ayant un état accessible par tous, sous tous les angles, chaque fonctionnalité doit se débrouiller pour maintenir chaque donnée qu'elle manipule dans un état cohérent. Ainsi, toutes les fonctionnalités sont en charge de maintenir l'état de chaque entité manipulée. Cela s’appelle un plat de spaghettis. On aurait pu avoir des objets implémentant le code métier qui contiennent aussi les données qu’ils manipulent. Vous vous souvenez de vos cours de programmation orientée objet, alors arrêtons d'écrire des procédures.

Vous connaissez la POO et SOLID ? La POO est simplement la Programmation Orientée Objet. SOLID est une série d'initiales qui décrivent des principes que nos designs objets devraient suivre : Single responsability, Open/closed, Liskov substitution, Interface segregation et Dependency inversion.

<h2>TDD</h2>

En décrivant un état (en diagramme de classes, en schéma de bases de données ou en format de données d'échanges), puis en écrivant un algorithme dans des classes extérieures à ce modèle, on écrit une procédure qui doit maintenir le modèle dans un état cohérent. Les évolutions suivantes s'ajouteront à cette procédure ou apparaîtront dans une autre procédure. Vous aurez besoin de tests automatisés pour prouver leur bon fonctionnement et l'absence de régressions.

Vous connaissez TDD ? Le Test Driven Development demande l'écriture d'un test exprimant ce que vous attendez de votre logiciel avant d'écrire le code correspondant. Les conséquences sont magiques ! Laissez-vous guider ! Chaque test décrit un problème à résoudre, une question ou un aspect de la fonctionnalité. Cela vous aide à décomposer votre problème en challenges individuels. Le code que vous concevez et écrivez ensuite doit faire passer les tests au vert, les uns après les autres. Sans TDD, votre conception doit répondre à tous les problèmes à la fois. En TDD, vous écrivez des tests pour chaque problème, puis votre conception et votre code devront résoudre chaque problème, faire passer chaque test l'un après l'autre.

<h2>Refactoring Legacy Code</h2>

Toutes les DSI ont leurs applications clefs. Elles les font évoluer jusqu'à que l'ajout de nouvelles fonctionnalités et/ou les corrections de bugs en production ne soient plus rentables. Effectivement, les logiciels âgés ont reçu des évolutions qui se sont empilées et imbriquées les unes dans les autres. Le code devient vite complexe à comprendre et fragile. Vous aurez besoin de mettre en place une bonne quantité de tests automatisés et de préférence instantanés sur le comportement de ces procédures pour pouvoir les revoir, les factoriser, les simplifier, bref, les maintenir sans régression.

Vous connaissez Refactoring Legacy Code ? Il s'agit d'un recueil de méthodes pour couvrir un code existant de tests pour montrer et conserver son fonctionnement d'origine et pouvoir ensuite le travailler pour le simplifier, le clarifier et comme son nom l'indique, le faire évoluer à nouveau.

<h2>DevOps</h2>

Les codes de build, de livraison, de déploiement et de mise en production sont souvent délaissés en pariant sur le fait qu'ils n’évolueront pas... D'une manière générale, on manipule des fichiers textes (du code) et quelques binaires (jars, dll, etc.) pour les transformer et les déplacer du poste d'un développeur à un serveur en production. Tout se passe sur des machines utilisées par des informaticiens... Et on livre tout cela plusieurs fois par an. Comment se fait-il que cela ne soit pas correctement automatisé ?

Vous connaissez DevOps ? Les développeurs apportent des évolutions en production, les opérationnels sont gardiens de la stabilité de la production... DevOps cherche a réconcilier ces deux mondes.

<h2>BDD</h2>

Je vois des équipes qui reçoivent des expressions de besoins venues du ciel : d'un client invisible, d'un BA/PO communiquant ses User Stories sans que les développeurs puissent en faire un retour, ni explorer le besoin avec un BA et un testeur pour bien comprendre ce besoin... C'est le genre de sujet qui va en production et revient plusieurs fois dans les mains des développeurs avant de pouvoir passer au sujet suivant.

Vous connaissez BDD ? Le Behavior Driven Development est une méthode de communication entre les Analystes, les Testeurs et les Développeurs pour se mettre d'accord sur ce que l'on attend du logiciel. Cette attente est décrite sous la forme d'une fonctionnalité, de scénarii et surtout d'exemples. L'essentiel du BDD est bien cette spécification par l'exemple. Les outils pour automatiser des tests à partir des spécification sont du bonus.

<h2>Pair Programming</h2>

L'autre symptôme similaire étant de travailler sur un métier complexe et d'envoyer des développeurs sur le code sans les avoir formés sur ce métier, que pouvons-nous attendre comme qualité d'un tel logiciel ? Que ce soit pour embarquer un nouveau développeur, pour réaliser un nouveau type de fonctionnalité, pour implémenter des choix structurants ou pour raccourcir vos délais de validation par des utilisateurs finaux, vous devrez travailler à plusieurs sur le sujet.

Vous connaissez le Pair-Programming ? Une pratique qui consiste à développer à deux sur le même poste de manière intelligente. En se passant régulièrement le clavier, les deux personnes sont et se maintiennent actives. Les deux doivent discuter de ce qu'ils font et, en l'exprimant, ils font face à des problèmes que l'on ignore parfois lorsque l'on est seul. Cette pratique permet aussi de diffuser les bonnes idées des uns et des autres sur leurs raccourcis clavier, leurs démarches, etc. C'est très enrichissant pour les novices comme pour les expérimentés.

<h2>Craftsmanship</h2>

Certains développeurs se sont regroupés dans des communautés de professionnels pour trouver des moyens de résoudre les problèmes ci-dessus. Elles ont trouvé des solutions et représentent le mouvement Software Craftsmanship. Nous sommes fiers d'en faire partie. Les solutions que j'ai juste nommées à la fin de chaque point sont les meilleures connues à ce jour par nos communautés et nous les prônerons jusqu'à ce que l'on en trouve de meilleures.

Si vous voulez reprendre le contrôle de vos logiciels et les développer pour qu'ils restent évolutifs le plus longtemps possible :

<ul>
    <li><strong>Rejoignez le mouvement en meetup <span style="color: #0000ff;"><a style="color: #0000ff;" href="http://www.meetup.com/paris-software-craftsmanship/" target="_blank">Software Crafts(wo)manship</a>.</span></strong></li>
    <li><strong>Venez vous entraîner en meetup <span style="color: #0000ff;"><a style="color: #0000ff;" href="http://www.meetup.com/Craft-your-Skills/" target="_blank">Craft Your Skills</a></span>.</strong></li>
    <li><strong>Venez voir <span style="color: #0000ff;"><a style="color: #0000ff;" href="http://www.parisjug.org/xwiki/wiki/oldversion/view/Meeting/20160209" target="_blank" class="a">une démo au Paris Jug à leur prochaine soirée Craftsmanship</a></span>.</strong></li>
</ul>

Histoire de ne pas vendre du rêve, les projets informatiques restent riches d'embûches et leurs réussites ne sont jamais gagnées d'avance. Il existe même d'autres problèmes qui n'ont pas de solution aujourd'hui : les entreprises et les formations considèrent que les développeurs doivent rapidement devenir experts, chefs de projet ou analystes, le nombre de développeurs double tous les 5 ans et son corollaire : la moitié des développeurs ont moins de 5 ans d'expérience... et j'en passe.

Autant le Craftsmanship devrait devenir la norme dans notre métier et les pratiques citées dans cet article devraient être enseignées en formation initiale, autant nous devrions toujours accueillir nos nouveaux et les embarquer dans une mise à niveau qu’elle soit technique et/ou métier. Comme dans tous les métiers, les jeunes diplômés manquent et manqueront toujours d'expérience et le problème se pose aussi lorsque l'on change de secteur (de la gestion vers la finance, par exemple). Contrairement aux autres métiers, le nôtre est jeune. Il reste encore beaucoup de sujets à défricher et de challenges à relever.

Vous êtes les bienvenus !
