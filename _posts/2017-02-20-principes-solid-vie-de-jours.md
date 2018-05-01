---
layout: post
title: Les principes SOLID dans la vie de tous les jours
date: 2017-02-20 11:48
author: raphael squelbut
comments: true
categories: [Bonnes pratiques de dév, Programmation, Traduction]
---
Cet article est une traduction d'un post de Erik Dietrich donnant quelques moyens mnémotechniques pour se rappeler les bons principes de développement. La version originale est parue ici : <a href="http://www.daedtech.com/visualization-mnemonics-for-software-principles/" title="visualization mnemonics for software principles">visualization mnemonics for software principles</a>.

<h1>Introduction</h1>

Vous aimez participer aux discussions techniques de vos collègues sur le développement logiciel, mais trop souvent, il vous faut regarder votre téléphone en cachette pour comprendre tous les concepts de la discussion. Ou alors peut-être avez-vous réussi à obtenir un entretien pour un poste de leader technique. Ce qui est sûr, c'est que vous aimez comprendre votre environnement de travail et faire votre métier le mieux possible. Pour cela, vous vous tenez informés de l'évolution du monde du développement et de ses principes de fabrication. En lisant des blogs sur les bonnes pratiques de développement, par exemple.

A travers l'écriture de ce post, j'aimerais vous fournir quelques rapides techniques pour apprendre les principes de développement et vous permettre de les mettre en pratique ensuite. Pour ce faire, utilisons quelques saynètes.

<h2>Loi de Demeter</h2>

La semaine dernière, j'étais tranquillement assis au volant de ma voiture quand je me suis arrêté à la station service pour me dégourdir les jambes et m'acheter quelque chose à boire. J’ai attrapé la canette dans le frigo, l'ai posée sur le comptoir et j'ai attendu que le caissier me dise

<blockquote>
  ça fera $1,95
</blockquote>

A ce moment-là, le plus naturellement du monde, j'ai enlevé mon pantalon. Le mec a commencé à hurler à l'indécence et a menacé d'appeler la police. Alors, confus, j'ai expliqué

<blockquote>
  Je suis justement en train d'essayer de vous payer! Regardez : je vous tends mon pantalon, vous n'avez qu'à fouiller mes poches, trouver mon portefeuille, le sortir et prendre l'argent nécessaire. S'il y a trop, remettez la monnaie dans le portefeuille, puis vous le remettez dans la poche du pantalon et enfin vous me rendez le pantalon.
</blockquote>

Là, le type a sorti un fusil de derrière le comptoir en me disant que dans sa boutique, soit on obéit à la <a href="https://fr.wikipedia.org/wiki/Loi_de_Déméter" target="_blank">loi de Demeter</a> soit on s'en va.

<img src="http://www.daedtech.com/wp-content/uploads/2014/06/PantlessLawOfDemeter.jpg" alt="illustration" title="Illustration de la loi de Demeter" />

Mais que dit la loi de Demeter ? Entre autre, elle dit ceci

<blockquote>
  donnez à vos collaborateurs exactement ce qu'ils demandent et ne leur fournissez pas quelque chose qu'ils vont devoir inspecter à la recherche de ce dont ils ont besoin.
</blockquote>

C'est la raison pour laquelle on ne tend pas son pantalon au caissier (ou même son portefeuille) mais qu'on lui donne directement l'argent. Il n'est pas pertinent de lui faire fouiller vos affaires à la recherche d'argent. La loi de Demeter vous encourage à faire de même pour votre code. Ne présentez pas votre pantalon au client de votre méthode en le forçant à chercher ce qui l'intéresse en invoquant <code>Pants.Pockets[1].Wallet.Money</code>, donnez-lui directement <code>Money</code>. Et si vous êtes le caissier, n'acceptez pas qu'on vous tende un pantalon pour y chercher vous-même la monnaie, demandez l'argent ou sortez votre fusil.

<h2>Single Responsibility Principle - SRP</h2>

Il y a peu de temps, ma femme et moi avons investi dans l'immobilier. Il s'agit d'une petite maison construite dans les années 50. Bien qu'agréable et charmante, elle ne dispose pas de toutes les commodités modernes dont j'aimerais profiter. J'ai donc dû me lancer dans quelques petits chantiers, refaire les sols, construire quelques trucs et en détruire d'autres. Ce genre de bricoles.

J'ai ainsi décidé d'installer un système d'élimination des déchets, celui-ci se composant de deux parties : la partie plomberie et la partie électricité. La partie plomberie s'est révélée relativement simple : il a suffi d'enlever le tuyau de vidange existant et d'insérer le nouveau dispositif entre la vidange et le tuyau. La partie électricité a été plus fastidieuse puisqu'il a fallu installer un interrupteur permettant d'allumer ou d'éteindre ce dispositif. Naturellement, je n'ai pas eu envie de créer de toute pièce un nouveau circuit allant du compteur général jusqu'à ma cuisine, juste pour cet interrupteur. J'ai donc décidé d'en utiliser un qui était déjà là : l'interrupteur de l'entrée, celui-ci servant justement à allumer ou éteindre la lumière de cette même entrée. Mais je lui ajouté une autre fonction : celle de contrôler mon broyeur.

Et c'est vrai que ça fonctionne super bien. Jusqu'à aujourd'hui, nous n'avons eu qu'un seul petit problème, un jour où nous recevions des amis à dîner. Je vidais les épluchures du repas que nous venions de préparer lorsqu'ils ont sonné. Ma femme s'est dépêchée de les accueillir et a allumé la lumière de l'entrée au moment précis où je faisais tomber une cuillère dans le dispositif. Heureusement, je n'ai eu à déplorer qu'une éraflure mineure et la perte d'une cuillère. Au final, ce n'est pas grand-chose à payer en comparaison de l'installation d'un tout nouveau circuit électrique. Mais pensez-vous réellement, qu'il s'agisse du pire qu'il puisse m'arriver ?

Nous sommes d'accord que la pire chose qui puisse arriver serait que l'un d'entre nous perde la main à cause de cette conception absurde. Parce que vous êtes allés à l'encontre du <em>SRP</em>, que l'on pourrait décrire grossièrement comme

<blockquote>
  faire une seule chose mais la faire bien
</blockquote>

ou bien

<blockquote>
  n'avoir qu'une seule raison de changer
</blockquote>

Or ici, nous avons 2 raisons d'utiliser l'interrupteur : allumer le broyeur ou éclairer l'entrée. Cela crée un problème évident! Et le parallèle avec le code est vrai. Si vous avez une classe qui doit être changée, lorsque le schéma évolue ou encore que la GUI évolue, alors vous avez une classe qui sert 2 maîtres. Dans ce cas, il est possible que les évolutions induites par une contrainte affectent l'autre responsabilité. Comme l'espace disque n'est pas très cher et que les <em>classes/namespaces/modules</em> sont des ressources renouvelables, dans le doute, n'hésitez pas, séparez!

<h2>Open/Closed Principle - OCP</h2>

Je n'ai pas vraiment eu le temps de regarder la télé ces derniers jours. Pour une bonne et simple raison : ma télé est terriblement chronophage. Tout allait bien à l'époque où la télé ne diffusait qu'un simple signal analogique qu'on captait depuis la prise murale. Mais le temps du tout-numérique est arrivé. J'ai donc du trafiquer ma télé et la recâbler pour qu'elle traite le signal. Le pire, ça a été quand je me suis fâché avec mon fournisseur d'accès et que nous sommes passés chez <em>DISH (fournisseur de télé par câble américain)</em>, j'ai dû rebricoler encore une fois ma télé. Maintenant, nous avons une Nintendo Wii, un lecteur de DVD et un <em>Roku (lecteur de streaming)</em>. Entre nous, j'aimerais vraiment savoir qui a le temps de bidouiller sa télé et de la recâbler pour qu'elle sache traiter chacun de ces nouveaux terminaux... Mais comme j'aime le challenge, j'ai essayé l'an dernier de <em>brancher</em> une vieille Sega MasterSystem. Mon terminal <em>Dish</em> n'a pas supporté, il a cessé de fonctionner.

…Sachez-le, jamais personne n'a fait ça. Pour la bonne et simple raison que personne ne vous explique jamais que votre télé a été conçue en respectant l'OCP. Ce principe signifie que vous devez fabriquer des composants qui sont fermés à la modification mais ouverts à l'extension. En d'autres termes, les télévisions ne sont pas prévues pour que vous les bricoliez vous-même. J'imagine aussi que personne ne conçoit devoir trifouiller les entrailles de sa télé juste pour brancher un composant. C'est d'ailleurs pour éviter cela que les câbles et connecteurs Coax/RCA/Component/HDMI/etc. ont été créés. Ces connecteurs et le fait que la télé soit scellée sous garantie induisent que votre télévision est ouverte à l'extension mais fermée à la modification. Vous pouvez étendre ses capacités en branchant le périphérique de votre choix, voire même en branchant des composants qui n'existent pas encore, comme par exemple une X-Box 12. C'est le même principe que je vous invite à suivre pour avoir du code flexible. Lorsque vous codez, efforcez vous de maximiser la flexibilité en facilitant la maintenance à l'aide d'extensions. Si vous programmez à l'aide d'interfaces ou que vous permettez de modifier le comportement à travers l'héritage, la vie sera beaucoup plus simple quand viendra l'heure du changement. Allez dans ce sens plutôt que d'écrire la bonne grosse classe <em>qui fait tout</em> et que vous allez devoir comprendre puis modifier à chaque sprint. C'est crado et vous allez finir par vraiment la détester, cette classe. Sa conception aussi. De la même manière que vous détesteriez la télévision décrite plus haut.

<h2>Liskov Substitution Principle - LSP</h2>

Le soir, j'aime dîner léger. J'ai donc pour habitude de manger une bonne salade bien classique. Vous savez, ce genre de salade avec des tomates, des croûtons, de l'oignon, des carottes et de la ciguë. Du coup, je mange ma salade d'une façon assez particulière : à chaque coup de fourchette, j'examine les éléments avant de les mettre dans ma bouche (beaucoup d'amateurs de salades composées vont beaucoup plus vite mais jouent avec leur vie selon moi). Pour ce faire, je déroule un algorithme simple. Si l'ingrédient que j'ai dans ma fourchette n'est pas de la ciguë, alors je le mange. Si c'est de la ciguë, je le repose dans un coin de mon assiette pour le jeter plus tard. C'est, à mon avis, la seule façon de manger votre salade de ciguë en toute sécurité.

Sinon, vous pouvez penser au LSP. Ce principe explique qu'en cas de relation d'héritage, chaque dérivé devrait pouvoir être remplacé par son type de base. Ainsi, dans ma salade de <em>comestibles</em>, je ne devrais pas avoir un type dérivé <em>cigüe</em> qui ne se comporte pas de la même façon que les autres <em>comestibles</em>, c'est à dire qu'il m'empoisonne. Autre cas, imaginons une collection d'objets hétérogènes dans une hiérarchie d'héritage. Dans ce cas-là, vous n'irez pas parcourir les éléments les uns après les autres et vous demander

<blockquote>
  voyons de quel type est celui-ci puis adaptons le traitement
</blockquote>

Alors respectez le LSP et ne préparez jamais une salade à la ciguë. Pour personne. De cette façon, vous aurez un code plus propre et vous éviterez la prison.

<h2>Interface Segregation Principle - ISP</h2>

Dieu soit loué le caching de pages web !!! Ça peut sauver des vies !!! Quand je consulte mon dictionnaire en ligne fétiche, <em>expertbeginnerdictionary.com</em> (ceci n'est pas un vrai site, pour ceux qui pensaient le visiter), on me demande un mot à rechercher. Dès que je le saisis, on m'envoie le dictionnaire à travers le réseau, puis avec Ctrl-F, je peux rechercher ma définition. Le problème, c'est que le navigateur met un temps infini à charger le dictionnaire en entier. Ça serait vraiment pénible sans ce mécanisme de cache. Le problème, c'est quand un mot du dictionnaire change. Dans ce cas-là, le cache est invalidé et ma prochaine recherche devra recharger le dictionnaire complet. Si seulement il y avait une meilleure façon de faire...

Mais en fait si, il y en a une qui ne renvoie pas tout le dictionnaire dès que je cherche un mot. Elle renvoie juste la définition de ce mot. Si je veux savoir ce qu'est un <em>zèbre</em>, je me moque de connaître la signification de <em>aardvark</em>. En plus, ma recherche de <em>zèbre</em> ne sera pas affectée par un quelconque changement sur le mot <em>aaedvark</em>. En principe, ma recherche ne devrait être dépendante que des mots et définitions que j'utilise au même moment, mais jamais de tout le dictionnaire. De la même manière, si vous définissez des interfaces publiques à destination de vos clients, essayez de les découper en parties les plus petites possibles et composables entre elles. Et laissez à votre client le soin de les assembler comme bon lui semble, plutôt que de le forcer à composer avec le tout (ou le dictionnaire). L'ISP précise qu'il ne faut pas forcer les clients d'une interface à dépendre d'une méthode dont ils n'ont pas besoin. Donnez à votre client le minimum syndical.

<h2>Dependency Inversion Principle - DIP</h2>

Avez-vous déjà visité une usine automobile ? C'est fou de voir la façon dont est assemblée une voiture. On part d'un chassis puis la voiture assemble son moteur, ses sièges, ses roues, etc. C'est vraiment incroyable à observer. Si vous voulez vous régaler, regardez ces sous-parties assembler elles-même leurs composants internes. Par exemple, le moteur construit son alternateur, sa batterie, sa transmission. C'est un coup de génie de l'industrie. Bien sûr, il y a un inconvénient à tout. Et aussi cool que cela puisse paraître, ça doit-être quand même un peu frustrant pour les ouvriers de n'avoir aucun contrôle sur ce moteur que la voiture s'est construit elle-même. Tout ce qu'ils peuvent faire, c'est dire

<blockquote>
  je veux une voiture
</blockquote>

et la voiture fait le reste.

Je parie que vous imaginez la base de code que je décris. Il y a longtemps, j'ai filé cette métaphore dans l'article <a href="http://www.daedtech.com/inverting-control/" title="Inverting control">ici</a>. Ici je vais résumer en disant qu'il s'agit du pattern "commande et contrôle" où les constructeurs d'objets instancient tous les objets dont ils dépendent. Ainsi le FooService instancie son propre logger. Cette façon de faire va à l'encontre du DIP. Le DIP préconise que les modules de haut niveau, comme <em>Voiture</em> par exemple, ne soient pas dépendants des modules de bas-niveau, ici le <em>Moteur</em>. Ils devraient plutôt dépendre d'une abstraction, ici ça serait l’interaction <em>Voiture-Moteur</em>. Cela permet de modifier indépendamment le moteur ou la voiture. Ça signifie que nos mécaniciens peuvent décider de quel moteur va dans quelle voiture. Comme décrit dans le lien, une base de code construite en respectant ce principe tend à être composable. Alors que celle basée sur le pattern "Commande et Contrôle", favorisant l'approche "voiture, construis-toi toi-même", ne le sera pas. Pour bien intégrer le DIP, demandez-vous qui doit décider des morceaux à embarquer dans la construction de la voiture. Les gens qui construisent la voiture ou alors la voiture elle-même ? Attention, une de ces deux idées est absurde.

…Cinq des principes décrits ici composent les principes SOLID. Pour en savoir plus sur le sujet, je vous invite à consulter les cours Pluralsight sur le sujet.
