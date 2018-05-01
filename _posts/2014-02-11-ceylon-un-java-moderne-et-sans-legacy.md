---
layout: post
title: "Ceylon : un java moderne et sans legacy"
date: 2014-02-11 16:49
author: nicolas fedou
comments: true
categories: [Actu, Ceylon, langage, Outils, Programmation]
---
<p style="text-align: justify;">J'ai récemment eu l'occasion d'assister à la conférence "Ceylon Tour Paris 2014".<img id="il_fi" style="padding-right: 8px; padding-top: 8px; padding-bottom: 8px;" alt="" src="http://ceylon-lang.org/images/ceylon-tour-paris-2014.png" width="167" height="95" /></p>

<p style="text-align: justify;">Je me suis intéressé à ce langage pour certains de ses objectifs :</p>

<ul style="text-align: justify;">
    <li>Être tellement simple à lire qu'un développeur connaissant l'Orienté Objet peut le comprendre sans apprendre le langage.</li>
    <li>Disposer d'un typage statique implicite.</li>
</ul>

<p style="text-align: justify;">Il présente d'autres caractéristiques intéressantes que j'ai découvertes à la conférence "Ceylon Tour Paris 2014" (et qui sont présentées sur la <a title="page d’accueil de leur site" href="http://ceylon-lang.org/" target="_blank">page d’accueil de leur site</a>). Par exemple, il doit :</p>

<ul style="text-align: justify;">
    <li>Tourner sur la JVM <strong><span style="text-decoration: underline;">et</span></strong> dans un moteur Javascript</li>
    <li>Être un modèle de composant (du modulaire qui montre les contrats et cache les implémentations)</li>
</ul>

<p style="text-align: justify;">Je me suis inscrit à cette conférence parce que Ceylon mérite que l'on s'y essaye et pour être honnête parce qu'elle était gratuite...</p>

<h1 style="text-align: justify;">L'écart entre Java 6,7 ou 8 et Ceylon</h1>

<p style="text-align: justify;">Ceylon ressemble à ce que serait Java s'il était à nouveau écrit aujourd'hui. Ils ont les mêmes cibles d'usage : être un langage Objet "généraliste", plutôt facile d'accès. Ceylon a quelques ambitions supplémentaires : il peut tourner sur la JVM mais aussi dans un moteur Javascript comme nos chers clients web.</p>

<h2 style="text-align: justify;">Modèle de données</h2>

<p style="text-align: justify;">Depuis l'apparition de Java, nous avons vu des logiciels de gestion :</p>

<ul style="text-align: justify;">
    <li>manipulant des documents qui factorisent leurs données dans des bases relationnelles,</li>
    <li>communiquant leurs données en xml,</li>
    <li>mettant à jour leurs pages web avec du Json...</li>
</ul>

<p style="text-align: justify;">Là où Java ne propose que des beans avec des champs et des relations qui sont <a href="http://www.agiledata.org/essays/impedanceMismatch.html">inadaptées</a> aux bases relationnelles, Ceylon propose d'écrire simplement des hiérarchies de données. Les arborescences DOM HTML en sont un bon exemple. Le système de typage est statique, fort et implicite. Du coup, on peut écrire des structures de données complexes sans s'arracher les oreilles ;-). Ces hiérarchies de données sont des déclaration d'instances imbriquées que l'on trouve dans la page qui présente <a href="http://ceylon-lang.org/documentation/1.0/tour/named-arguments/">les arguments nommés</a>. Tout en bas de cette page, l'avant dernier chapitre montre un exemple d'utilisation du module html :</p>

[java]
/* Attention, ce n'est pas un DSL, c'est du code Ceylon typé et compilé en bytecode java ou javascript */
Html {
  doctype = html5;
  Head {
    title = &quot;Ceylon: home page&quot;;
    Link {
      rel = stylesheet;
      type = css;
      href = &quot;/styles/screen.css&quot;;
      id = &quot;stylesheet&quot;;
    };
  };
  Body {
    H2 ( &quot;Welcome to Ceylon ``language.version``!&quot; ),
    P ( &quot;Now get your code on !&quot; );
  };
};
[/java]

<h2 style="text-align: justify;">Modèle de flux d'exécution (threads et concurrence)</h2>

<p style="text-align: justify;">Depuis l'apparition de Java, nous avons vu la volumétrie des données exploser, et les capacités de calculs se multiplier grâce aux processeurs multi-cœurs. Cela soulève deux problèmes que Java ne peut résoudre : avoir des structures de données naturellement "thread safe" et une capacité à manipuler les traitements comme on traite les données.</p>

<p style="text-align: justify;">Ceylon pour cela propose que ses "variables" soient immutables par défaut et mutables par annotation. Pour être clair, c'est la notion inverse en Java où les variables sont mutables par défaut en immutables avec le mot clé "final". Ainsi Ceylon manipule des "valeurs" :</p>

[java]
value rndValue = Math.random(); // rndValue est immutable.
variable value rndIteration = Math.random(); // rndIteration peut être assignée avec une nouvelle valeur.
[/java]

<p style="text-align: justify;">Pour les amateurs de DDD et/ou de programmation fonctionnelle, sachez que les Value Object et les paramètres immutables sont la norme.</p>

<p style="text-align: justify;">Pour Ceylon, tout a une référence, y compris les fonctions que l'on peut assigner à des "valeurs", passer en paramètres ou retourner par nos fonctions. On peut ainsi propager des traitements d'une classe à une autre, voire d'un thread à un autre sans le formalisme des Single Abstract Method ou des classes implémentant "Runnable". Les "compréhensions" en sont une conséquence naturelle et pourtant exotique pour moi.  <a href="http://ceylon-lang.org/documentation/current/introduction/#comprehensions" target="_blank">Une "compréhension" </a>est l'initialisation d'une collection, map ou tableau, avec une expression retournant une collection du bon type... La collection peut donc être une map et l'expression être des fonctions de filter et/ou reduce. Grâce à ces compréhensions, Ceylon n'a pas besoin d'une nouvelle syntaxe lambda, ni de java 8 pour fournir du map/filter/reduce.</p>

[java]
value adults = [ for(p in people) if (p.age&gt;=18) p ]
[/java]

<h1 style="text-align: justify;">L'expressivité du langage</h1>

<p style="text-align: justify;">Bref, Ceylon est une version moderne de Java depuis ses fondations !</p>

<p style="text-align: justify;">Voilà pour ce qu'est Ceylon. Revenons un peu sur le typage statique et implicite.</p>

<p style="text-align: justify;">Les attributs (variables immutables) sont définis avec le mot clef value ou avec un type. On peut écrire tout un algorithme en n'utilisant que des values et le compilateur fait un maximum d'inférences de type. Notre code perd alors toutes ces déclarations de types qui alourdissent le code tout en gardant les erreurs de compilation lorsque l'on se trompe. <strong>C'est un typage implicite.</strong></p>

<p style="text-align: justify;">Ensuite, les API retournent des types que l'on ne verra pas dans le code mais qui apparaissent en infobulle lorsque l'on survole chaque mot clef "value". Ainsi, le compilateur vérifie tous les types même implicites. <strong>C'est donc un typage statique.</strong></p>

<p style="text-align: justify;">Enfin, ils ont poussé la logique du statique et implicite assez loin et c'est fort de café.</p>

<p style="text-align: justify;">Ainsi, lorsqu'une liste est initialisée avec des constantes en chaine de caractères et en nombre entiers, le type de la liste est l'union des types : "String|Integer". Une "value" ayant un type uni est une instance d'un seul de ces types. On ne peut alors utiliser que les méthodes communes à tous les types de l'union. L'autre option est d'itérer sur cette liste composée de plusieurs types et/ou de faire un "switch case" sur ces unions de types :</p>

[java]
void printDouble(String|Integer|Float val) {
  String|Integer|Float double;
  switch (val)
    case (is String) { double = val+val; }
    case (is Integer) { double = 2*val; }
    case (is Float) { double = 2.0*val; }
    print(&quot;double ``val`` is ``double``&quot;);
}
[/java]

<p style="text-align: justify;">Le principe inverse existe avec des intersections de types où chaque instance est à la fois d'un type et d'un autre. On peut alors utiliser les méthodes des deux types. Je n'ai pas vu comment se passe cet héritage multiple dans le détail.</p>

<p style="text-align: justify;">J'ai eu une surprise en voyant que Object est un type père de tous, mais fils de Anything et que ce dernier est père de Object et de Null. Null n'ayant qu'une seule instance en mémoire (null). Ainsi, les méthodes pouvant retourner une valeur nulle retournent leur type de valeur naturel ou Null. Cela s'écrit évidement "String|Null" ou en raccourci String? :</p>

[java]
String? name = process.arguments[0];
if (exists name) {
  String notNullable = name;
  print(&quot;hello &quot; + notNullable);
}
[/java]

<p style="text-align: justify;">Cela signifie qu'une valeur pouvant être nulle (String?) et une valeur ne pouvant pas être nulle (String) n'ont pas strictement le même type.</p>

<p style="text-align: justify;">J'ai eu deux dernières bonnes surprises. D'une part, les listes vides ont aussi leur type comme les valeurs nulles, les iterateurs finissent leurs itérations avec le type "Finished", ...</p>

[java]
{String*} noStrings = {}; // {String*} est un iterable pouvant être vide et retourner Finsihed tout de suite
{String+} twoStrings = {&quot;hello&quot;, &quot;world&quot;}; // two Strings ne peut pas être un itérable vide
[/java]

<p style="text-align: justify;">D'autre part, des assertions permettent de faire le ménage. Comme le compilateur ne peut pas toujours être sûr de tous les types, il prends les assertions et autres casts comme des preuves. Par exemple, dans la cas précédent, le "name" est déclaré avec un point d’interrogation et cela signifie qu'il peut être nul. L'instruction "exists" qui suit teste que "name" n'est pas nul. Le système de typage de Ceylon est alors sûr que "name" ne peut pas être "nul" et la déclaration de "notNullable" sans le point d’interrogation est valide.</p>

<h1 style="text-align: justify;">La portée des cas d'usages</h1>

<p style="text-align: justify;">Je me servirai de ce langage pour écrire rapidement des outils évolués mais internes à notre équipe comme des outils de reporting qui remontent quelques info provenant des logs mis sous ElasticSearch, des états des derniers build Jenkins, le bon déroulement de batchs de déploiement, etc... bref, ce que l'on appelle parfois les outils ou les "toy projects" professionnels.</p>

<p style="text-align: justify;">Le site annonce une version 1.0.0 qui est prête pour de la production. Autant ces gens sont sérieux et crédibles, autant je ne conseille à personne de lancer un projet "sensible" sur une technologie avec laquelle il n'a pas suffisamment d'expérience. C'est d'ailleurs l'intérêt des projets d'outillage. Ils ont non seulement une taille et un coût raisonnable mais, en plus, ils partent en production...</p>

<h1 style="text-align: justify;">L'état actuel</h1>

<p style="text-align: justify;">Enfin, pour ce qui est de la conférence/formation, elle nous a fait faire un vaste tour de ce qu'est Ceylon, de son écosystème et de l'état d'avancement de chaque fonctionnalité.</p>

<p style="text-align: justify;">En effet, le langage est en version 1.0 "No More Mr Nice Guy" mais pas son environnement. Les plugins de l'IDE Eclipse sont au point mais écrits en Java. Le système de build concurrent du Maven de Java et du Gradle de Groovy est en pleine ré-écriture. Cayla est un framework web prometteur côté serveur ET client qui manque de templates, de widget (une taglib, du jsf, etc...).</p>

<h1 style="text-align: justify;">Et dans le futur</h1>

<p style="text-align: justify;">En conclusion, je vois dans Ceylon, un phare pouvant indiquer une direction aux développeurs de Java. J'y vois aussi un langage plus expressif et potentiellement plus facile à écrire pour les développeurs d'applications métiers.</p>

<p style="text-align: justify;">Le langage est enfin là, il faut maintenant qu'il trouve son public, qu'on trouve des patterns et des bonnes pratiques (comme l'usage de méthodes lazy dans les opérations sur les collections). J’espère voir fleurir des ateliers orientés sur l'usage du langage que ce soit sous forme de Kata ou d'application démo comme le fameux "Pet Shop" de "JEE".</p>

<p style="text-align: justify;">L'avenir fera ses choix en temps et en heure, mais si jamais Oracle voulait se concentrer sur la JVM et ses outils de contrôle (JMC, JProfiler, etc.) et abandonner Java, cela ne m’inquièterait plus, la relève est déjà là !</p>
