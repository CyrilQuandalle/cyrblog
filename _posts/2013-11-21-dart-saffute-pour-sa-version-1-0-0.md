---
layout: post
title: Dart s'affûte pour sa version 1.0.0
date: 2013-11-21 09:54
author: matthieu bertin
comments: true
categories: [Actu, dart, google, Outils, Programmation, web]
---
<p style="text-align: justify;"><img class="alignleft" alt="Logo Dart" src="http://www.arolla.fr/blog/wp-content/uploads/2013/11/dartlang.png" width="100" height="100" />Il y a déjà deux ans que Google a dévoilé son idée de remplacer le JavaScript par un langage appelé Dart. Nous attendions la sortie de la version 1 depuis le dernier Google I/O, et Google a profité du Devoxx pour la lancer. Il était donc temps de se pencher sur ce nouveau langage et de s'en faire une idée.</p>

<p style="text-align: justify;"><!--more--></p>

<p style="text-align: justify;"><strong>Les Outils de développement</strong>
Le SDK est proposé avec un éditeur léger (<a title="Dart Editor" href="https://www.dartlang.org/tools/editor/">DartEditor</a>) basé sur Eclipse. Il est effectivement très léger et on ne peut lui reprocher la lenteur ou le poids d’Eclipse. Il permet d'avoir rapidement les outils nécessaires pour essayer Dart, mais plusieurs accessoires manquent (le formatage automatique de code manque cruellement) et il peut être préférable d'utiliser le plugin Dart d'IntelliJ.
En attendant, le SDK est capable de « transpiler » le code Dart en code Javascript, mais avec un coût de performance et encore quelques limitations par rapport à du code Dart exécuté nativement (notamment du côté du multithreading et de la communication inter-process qui se limite à la transmission de types primitifs).</p>

<p style="text-align: justify;"><strong>La syntaxe, point fort de Dart</strong>
La syntaxe Dart a été pensée en reprenant de très bonnes idées dans plusieurs langages et dans des APIs populaires. Tout d'abord, il est possible d'utiliser un typage fort statique - qui peut provoquer des warnings ou des erreurs à l'inspection selon la configuration des outils, ce qui permet à des adeptes de langages stricts de garder quelques réflexes. Il s'agit tout de même d'un compromis - Dart reste un langage dynamique, et il y a tout de même des inconvénients majeurs parfois ennuyeux comme l'absence de surcharge.
Les syntaxes des accesseurs ont repris des bonnes idées: au premier abord on peut penser qu'accéder directement aux variables est une mauvaise pratique, mais la syntaxe proche des concepts d'Objective-C permet de redéfinir les champs sans modifier l'interface: lorsqu'on accède à un champ, on passe en fait à travers le getter, qu'il soit un getter par défaut ou redéfini à la main.</p>

[java]
class Foo {
  // l'absence de getter &amp; setter pour cette variable implique des getters &amp; setters par défaut,
  //comme en Java l'absence de constructeur implique un constructeur sans paramètres par défaut.
  String fooStringField;

  int fooIntField; // ceci est un nouveau champ, résultant d'un refactoring du champ formerIntField.

  @deprecated
  num get formerIntField =&gt; this.fooIntField;

  // ces deux getters &amp; setters permettent de garder la compatibilité,
  // tout en les marquant comme dépréciés pour de futurs refactorings.
  @deprecated
  set formerIntField(num value) =&gt; this.fooIntField = value.toInt();

  // Ce constructeur est un raccourci évitant un corps de fonction
  // ne contenant que des lignes this.field = this.fieldValue;
  Foo(this.fooStringField, this.fooIntField);

  // un deuxième constructeur: n'ayant pas de surcharge, il faut nommer les constructeurs différemment.
  Foo.NamedConstructor() =&gt; this.fooIntField = 0, this.fooStringField=&quot;this instance has a field equals to ${this.fooIntField}&quot;;
}
[/java]

<p style="text-align: justify;">Une écriture équivalente en Java donnerait ce qui suit:</p>

[java]class Foo {

  private String fooStringField;

  private int fooIntField;

  public Foo() {}

  public Foo(String fooStringField, int fooIntField) {
    this.fooIntField = fooIntField;
    this.fooStringField = fooStringField;
  }

  @Deprecated
  public Foo(String fooStringField, float formerFloatField) {
    this(fooStringField, (int) Math.round(formerFloatField));
  }

  public static Foo namedConstructor() {
    int intFieldValue = 0;
    return new Foo(&quot;this instance has a field equals to &quot; + intFieldValue, intFieldValue);
  }

  @Deprecated
  public float getFormerFloatField() {
    return this.fooIntField;
  }

  @Deprecated
  public Foo setFormerFloatField(float value) {
    this.fooIntField = (int) Math.round(value);
    return this;
  }

  public Foo setFooIntField(int value) {
    this.fooIntField = value;
    return this;
  }

  public int getFooIntField() {
    return this.fooIntField;
  }

}
[/java]

<p style="text-align: justify;">Outre l'aspect économique en lignes de codes, qui est un argument un peu fallacieux maintenant que l'auto-complétion se retrouve un peu partout, on retrouve intégré au langage un certain nombre de constructions qui sont considérées comme de bonnes pratiques de développement en Java. Par exemple, l'opérateur cascade permet d’intégrer un pattern Builder au langage, ce qui est un plus non négligeable et permet une écriture élégante lorsqu'on initialise ses objets.</p>

[java]
// l'opérateur .. s'appelle l'opérateur cascade et se comporte comme un setter qui renverrait this.
Foo bar = new Foo()
    ..fooIntField=1
    ..fooStringField=&quot;a string value&quot;;
[/java]

<p style="text-align: justify;"><a href="https://www.dartlang.org/docs/cookbook/">Vous retrouverez ici une liste des idiomes Dart</a> choisis pour leur pertinence. Cette page est un bon point de départ pour des Javaistes qui y trouveront une bonne base de syntaxes "exotiques" à comprendre avant de se lancer dans Dart, ou de se faire un avis personnel sur sa syntaxe.
Côté APIs, il y a aussi de très bonnes surprises. On y retrouve les nouveautés de Java 8 (des APIs sur les collections construites sur le modèle de Guava); un modèle asynchrone basé sur les Futures (Java 7) ; ou encore un DOM accessible à la façon de jQuery (<code>document.query('.fooClass')</code>). Les technos qui ont fait l’unanimité se retrouvent condensées en une trentaine de modules bien <a title="documentation Dart" href="http://api.dartlang.org/docs/releases/latest/">documentés</a>.
Sur d’autres points trop complexes pour faire l’unanimité, Dart propose des modèles simplifiés. Le multithreading par exemple est très (trop?) simple pour éviter des problèmes trop fréquents ou trop complexes ; il n'existe pas pour le moment de mémoire partagée, ce qui évite les deadlocks – chaque <em>isolate</em>  ressemble à un acteur simple qui ne peut communiquer avec d’autres isolates qu’au travers de pipes.</p>

<p style="text-align: justify;"><strong>La publication de package, un autre atout de Dart</strong>
Semblable aux artefacts Maven, ou aux packages de PIP, Dart a à sa disposition un repository de packages sur lequel on peut y publier ses librairies : <a title="Packages Dart" href="http://pub.dartlang.org">http://pub.dartlang.org</a>. L’outil est inclus dans le SDK et s’annonce comme un standard pour gérer ses dépendances dans un projet Dart. Il ne s’agit pas d’un plus par rapport à JavaScript pour lequel plusieurs projets proposent un service comparable, mais il a l’avantage de proposer dès le départ un standard, ce qui, on peut l’espérer, permettra de retrouver une grande palette de librairies sur un seul système de packaging. On retrouve déjà des portages de librairies JS populaires comme <a href="http://pub.dartlang.org/packages/angular">Angular</a>, <a href="https://pub.dartlang.org/packages/polymer">Polymer</a>, <a href="http://pub.dartlang.org/packages/bootjack">Boostrap</a>... Ce qui diminue de beaucoup l'effort d'adoption pour des développeurs habitués à travailler avec Javascript.</p>

<p style="text-align: justify;"><strong>Et en pratique, qu’est-ce que cela donne ?</strong>
Dart est conçu pour être utilisé sur des projets Web, embarqué dans la page ou côté serveur. Et sur le côté Web, Dart doit malheureusement composer avec les limitations des navigateurs, qui ne sont pas adaptés au multithreading. Certes, la documentation de Dart ne ment pas – on peut effectivement lancer plusieurs threads qui s’exécuteront au mieux sur l’ensemble des cœurs disponibles – mais sans accès au DOM. Lorsque l’on veut découper le travail sur le DOM en plusieurs isolates, les isolates ne pourront accéder que séquentiellement au DOM – il faut donc attendre qu’un isolate dorme ou meure pour qu’un autre puisse commencer son travail. On peut par contre tout à fait séparer des travaux lourds dans des isolates n’ayant pas accès au DOM qui eux pourront s’exécuter sans se compromettre avec des limitations du navigateur, lorsque le code s’exécute au sein d’une VM Dart. L'utilité reste limitée de ce côté la.
Le dernier argument en défaveur de Dart est son adoption dans les navigateurs, en particulier pour son exécution native: il n’existe qu’un seul navigateur embarquant la VM Dart : Dartium. Il s’agit d’un prototype basé sur Chrome, qui pour le moment n’embarque pas la VM Dart dans les versions publiques, ce qui peut paraître étrange comme stratégie. Il faudra attendre que cette VM sorte sur Chrome ainsi que sur les autres navigateurs avant de profiter de tout le potentiel de Dart. Ce serait sans compter l'outil de transpilation pour générer du code Javascript, qui fonctionne déjà très bien: <a title="Dart Performance" href="https://www.dartlang.org/performance/">au vu des derniers benchmarks</a>, il n'est pas nécessaire d'attendre l'adoption par les navigateurs d'une machine virtuelle pour s'attendre à des gains de performance significatifs.</p>

<p style="text-align: justify;"><strong>Fléchette en mousse? En tous cas, <strong>Dart pointe une bonne direction</strong></strong>
Dart vaut-il le détour ? A titre personnel, je pense que oui. C’est un langage élégant et efficace. Il parait être un remplaçant prometteur pour Javascript côté Web, et pourrait aussi devenir un très bon langage de script pour épauler des plateformes plus strictes comme Java EE. La prise en main par un développeur Java sera facile, et un développeur Javascript n'y verra même pas d'effort à fournir, ce qui devrait jouer fortement en sa faveur. La présence de portages de plusieurs frameworks importants avant même la sortie en version 1.0 dénote que le langage s'accommode facilement des librairies les plus connues sans difficulté, et qu'il suscite un réel intérêt. Ce n’est peut être pas encore le moment de l’utiliser dans de grands projets, mais il faut voir dans de petits projets l'opportunité de se frotter à ce nouveau venu. Sans pouvoirs divinatoires, il est évidemment impossible de savoir ce qu’il adviendra de Dart dans le futur – mais on ne pourra lui reprocher d’être techniquement bancal ou de ne pas présenter assez d’innovations dès aujourd'hui.</p>

<p style="text-align: justify;"><strong></strong><a title="Site Dart" href="http://www.dartlang.org">http://www.dartlang.org</a></p>
