---
layout: post
title: Dependency Obsession
date: 2012-07-18 17:00
author: cyrille martraire
comments: true
categories: [ACL, architecture, aware, Bonnes pratiques de dév, Bounded Context, couches, couplage, craftsmanship, DDD, delicious, demeter, dépendances, DI, DRY, fragile, hygiène, IoC, layers, MVC, obsession, patterns, Programmation, software, software factory, SOLID, TDD, testabilité]
---
<p style="text-align: justify;">Je vais vous faire une confidence, je suis un véritable obsédé. Dans tous mes projets, j'y pense tout le temps, et la plupart de mes décisions de code, de design et d'architecture sont influencées par cette obsession. Vous l'avez deviné, mon obsession tourne autour des dépendances entre les éléments du code, et je pense que vous y êtes sensibles aussi !</p>

<h2><strong>Plus de dépendances, plus de fragilité<a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/Fragile.png"><img class="alignleft size-medium wp-image-899" title="Fragile" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/Fragile-300x260.png" alt="" width="240" height="208" /></a></strong></h2>

<p style="text-align: justify;">Dans le langage courant, une dépendance est une relation de subordination, de sujétion (soumission), ou bien un état d'asservissement à une drogue (asservissement) ; dans les deux cas ce n'est pas désirable !</p>

<p style="text-align: justify;">Dans notre code, une dépendance signifie que tout changement de cette dépendance risque d'entraîner des impacts sur notre code, qui vont de l'erreur de compilation au bug qui apparaîtra tardivement en production.</p>

<p style="text-align: justify;">En particulier, les montées de versions d'une dépendance entraînent typiquement des cascades d'effets et de surprises qui peuvent facilement prendre des semaines à corriger. Quelle perte de temps et de motivation ! A l'échelle d'un système d'information entier, on sera donc contraint à monter en version toutes les applications qui dépendent de l'application qu'on souhaite vraiment déployer : un bon vieux Big Bang avec toutes les bonnes surprises qui vont avec !</p>

<p style="text-align: justify;">De façon générale, une classe isolée, c'est-à-dire sans aucune dépendance, ne coûte que sa propre maintenance (qu'on peut souvent annuler en vertu du principe Open/Close), alors qu'une classe qui dépend d'une autre classe nécessitera de l'effort de maintenance pour répondre à chaque changement de sa dépendance. L'augmentation du nombre de dépendances multiplie donc le coût de maintenance de cette classe, et ceci est valable pour chaque classe. En somme, plus une classe a de dépendances, plus elle est fragile, et par conséquent coûteuse à maintenir.</p>

<p style="text-align: justify;">Et encore, on ne parle ici que des dépendances directes, car les dépendances ont à leur tour leurs propres dépendances et ainsi de suite. Si, par malheur, on manipule explicitement les dépendances d'une dépendance, alors on augmente encore la fragilité, et c'est justement ce qu'interdit la loi de Demeter. Chaque classe bien élevée doit au moins absorber les impacts de ses dépendances directes.</p>

<p style="text-align: justify;">En définitive, les dépendances, bien que certaines soient inévitables, sont de véritables boulets qui nous empêchent de courir et finissent parfois même à nous paralyser.</p>

<h2><strong>Les dépendances, les boulets de nos projets</strong></h2>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/PortraitSuperBoulet.png"><img class="alignleft size-medium wp-image-900" title="PortraitSuperBoulet" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/PortraitSuperBoulet-212x300.png" alt="" width="212" height="300" /></a></p>

<p style="text-align: justify;">Les dépendances se traduisent aussi par des efforts supplémentaires pour tester une unité de code, car il faut alors fournir les dépendances d'une façon ou d'une autre. Dans le cas idéal, une instanciation suffit, mais dans le cas des dépendances récalcitrantes, comme souvent ce qui touche au middleware, alors tester devient rapidement un véritable chantier, où il faut sortir l'arsenal lourd avec les bazookas de mock surpuissants. Au pire, ce sont les dépendances qui vont parfois rendre purement impossible de tester hors système.</p>

<p style="text-align: justify;">Quand les dépendances échappent à tout contrôle, alors les cycles apparaissent et avec eux la joie des builds à plusieurs passes, parfois même non déterministes.</p>

<p style="text-align: justify;">Il n'est alors plus possible de ré-utiliser les livrables d'un module déjà construit, il faut donc systématiquement tout reconstruire. Un secret essentiel des builds rapides est déjà de garantir l'absence de cycles !</p>

<p style="text-align: justify;">Si les dépendances sont aussi vicieuses, pourquoi n'en parle- t-on  pas davantage ? La réponse est que... on en parle, tout le monde en parle, mais derrière des techniques diverses qui focalisent l'attention au point de parfois cacher leur véritable enjeu : garder le contrôle sur les dépendances !</p>

<h2 style="text-align: justify;"><strong>Une question d'hygiène</strong></h2>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/dents.jpg"><img class="alignleft size-medium wp-image-901" title="dents" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/dents-300x208.jpg" alt="" width="300" height="208" /></a></p>

<p style="text-align: justify;">Une part majeure des progrès en matière de santé est directement liée aux progrès de notre hygiène ordinaire, et c'est la même chose en développement logiciel ! De façon générale, décréter que "cette classe A ne doit dépendre de cette autre classe B" équivaut à dire que "<strong>A ne doit pas connaître B</strong>". L'idée bien sûr est de garantir que tout changement de B n'aura aucun impact sur A.</p>

<p style="text-align: justify;">Certains principes de base s'appliquent tout d'abord, guidés par l'économie de maintenance du code : le peu stable doit dépendre du stable, et non l'inverse. En particulier, le concret doit dépendre de l'abstrait, et non l'inverse : une interface ne doit pas dépendre de ses implémentations !</p>

<p style="text-align: justify;">Sur les 5 principes dits <strong>SOLID</strong>, le ISP (Interface Segregation Principle) vise à dépendre de l'interface la plus petite possible, tandis que l'incontournable DIP (Dependency Inversion Principle) est la technique charnière pour inverser la direction entre deux dépendances, à maîtriser sur le bout des doigts pour rester le maître de ses dépendances. Le LSP (Liskov Substitution Principle) définit le contrat qui permet de dépendre d'une interface sans se faire avoir par une implémentation.</p>

<p style="text-align: justify;">Il est aussi bon de savoir que le principe <strong>DRY</strong> (Don't Repeat Yourself) appliqué sans discernement accroît le couplage, et donc peut aggraver les soucis de dépendances.</p>

<p style="text-align: justify;">Enfin du côté de <strong>Domain-Driven Design</strong>, on distingue les Value Objects, les Entities et les Services, et là encore cela peut impliquer des conséquences en termes de dépendances, par exemple un Value Object ne devrait en général pas dépendre d'Entities ou de Services.</p>

<p style="text-align: justify;">Misko Hevery parlerait là des "<em>Newables</em>" par opposition aux "<em>Injectables</em>", même si on n'évoquera pas ici l'IoC et l'idée de Dependency Inversion, faute de place.
<strong></strong></p>

<h1 style="text-align: justify;"><strong>La maîtrise des dépendances, la clé des techniques d'ingénierie logicielle</strong></h1>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/weaponry.jpg"><img class="alignnone size-medium wp-image-911" title="weaponry" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/weaponry-300x246.jpg" alt="" width="300" height="246" /></a></p>

<p style="text-align: justify;">Vous connaissez les <strong>design patterns</strong> comme solutions de problèmes, mais les patterns sont aussi des solutions classiques à des questions de dépendances; par exemple un Proxy connaît l'interface proxiée, mais surtout pas le contraire, de même entre un Decorator et l'interface décorée, un Adapter ou une Facade et les éléments adaptés. Et côté code utilisateur, il n'est pas question de dépendre de la classe Proxy, Decorator, mais seulement de leur interface.</p>

<p style="text-align: justify;">Le fameux pattern <strong>MVC</strong> et ses amis décrit avant tout des restrictions de dépendances. Le Model (M) ne doit connaître ni le Controller (C) ni la vue (V), et la vue ne doit pas non plus connaître le Controller. Quelles que soient les variations du pattern MVC, ces restrictions représentent l'essence même du pattern.</p>

<p style="text-align: justify;">Les<strong> architectures "en couche"</strong> ne sont pas autre chose que des restrictions de dépendances : une couche n'a le droit de dépendre, de façon stricte ou relaxée, que des couches inférieures. On retrouve souvent l'ordonnancement des couches UI -&gt; Services -&gt; Data (approche non orientée object contrairement aux apparences), ou encore les couches UI -&gt; application -&gt; Domain, avec Infrastructure -&gt; Domain (DIP en action)</p>

<p style="text-align: justify;">L'<strong>architecture Hexagonale</strong> (aka Ports and Adapters, ou Onion), vivement recommandée dès que possible, va encore plus loin et consiste pour l'essentiel à garantir que le modèle métier au cœur de l'architecture (couche de domaine) ne dépend d'absolument rien. Hormis les primitives et les classes de base de votre langage, Nada. Nothing. Nichts. Au prix de cette discipline, des avantages énormes, en particulier la capacité de choisir ses dépendances en dehors du modèle. Ensuite une couche applicative fine, optionnelle, accède au domaine de modèle mais ne doit pas connaître le code de présentation (GUI, web-app...).</p>

<p style="text-align: justify;">Enfin les adapteurs (aka ACL), qui eux connaissent tout le monde mais ne contiennent aucune logique, font le lien et concentrent délibérément toute la fragilité. En cas de changement, ces adapteurs devront certainement absorber ce changement, pour éviter de les répercuter au reste du système, tels des absorbeurs de chocs.</p>

<h2 style="text-align: justify;"><strong>Le bonheur est dans la chasse aux dépendances</strong></h2>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/chasse_dahu.jpg"><img class="alignnone size-medium wp-image-916" title="chasse_dahu" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/chasse_dahu-224x300.jpg" alt="" width="224" height="300" /></a></p>

<p style="text-align: justify;">L'obsession des dépendances peut s'étendre à tous les aspects, y compris les configurations en XML, par annotations Java ou par attributs .Net. Un nom de classe dans un fichier XML rend ce fichier dépendant de la classe, par exemple en cas de renommage de la classe. Même dans la définition des modules parents ou composite Maven, on s'expose au risque de dépendances inutiles, voire de cycles, avec les types de mêmes périls que dans le code.</p>

<p style="text-align: justify;">A l'échelle plus macro, entre des <strong>Bounded Contexts</strong> qui peuvent représenter des équipes voisines ou lointaines, les techniques de Strategic Design de DDD s'inscrivent aussi dans cette obsession des dépendances pour raisonner sur les relations de dépendances (relation de type Conformist) ou au contraire d'affranchissement (Separate Ways, Anti-Corruption Layer) entre ces contextes.</p>

<p style="text-align: justify;">On ne saurait exagérer l'importance du sujet des dépendances en général, une obsession vertueuse pour le développeur logiciel professionnel. Toutes les techniques qui aident à minimiser les dépendances, dont TDD, sont bonnes à prendre et à apprendre. Toutes les techniques que vous connaissez peuvent se redécouvrir sous l'angle des restrictions de dépendances qu'elles imposent.</p>

<em>Retrouvez les références 100% dépendances sur la stack delicious : </em><em>http://www.delicious.com/stacks/view/NyDUvw</em>

<p style="text-align: justify;"></p>
