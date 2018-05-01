---
layout: post
title: Les fonctions et les fonctions d’ordre supérieur
date: 2013-01-25 18:55
author: nouhoum traore
comments: true
categories: [filter, fonction, Fonctionnel, fonctionnel, map, Programmation, scala]
---
La programmation fonctionnelle a de nombreux attraits dont le traitement réservé aux fonctions. Dans beaucoup de langages de programmation les fonctions ont un statut particulier. Par exemple en Java il est possible de créer un entier, de l’assigner à une variable ou de le passer comme argument à une autre fonction mais on ne peut pas de même avec les fonctions. En revanche les fonctions jouent un rôle central dans la programmation fonctionnelle et ne sont pas traitées comme des citoyens de seconde zone par les langages dits fonctionnels (Haskell, Caml, Clojure etc.). Cela est important pour l’écriture de certains types d’abstractions bien utiles et courantes en développement comme nous le verrons dans la suite mais avant d’aller plus loin définissons une fonction.
<h2>C’est quoi une fonction ?</h2>
Ce qu’on entend par fonction varie selon les langages de programmation mais la définition que je vais donner ici est suffisante pour les propos de l’article. Une fonction est une <strong><em>transformation</em></strong> qui produit une valeur à partir d’un ensemble de paramètres, les arguments de la fonction.
Les choses seront plus claires avec des exemples. Si nous notons par <strong>x</strong> un nombre entier quelconque. <strong>x</strong> peut donc prendre comme valeur <strong>0</strong>, <strong>1</strong>, <strong>2</strong> etc. nous pouvons définir la fonction <strong>f</strong> comme suit :
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-2K5h0CF43rQ/UNDr6ngGs6I/AAAAAAAAAyU/EOMVnocfFoM/s1600/f_double_fn.png"><img src="http://3.bp.blogspot.com/-2K5h0CF43rQ/UNDr6ngGs6I/AAAAAAAAAyU/EOMVnocfFoM/s400/f_double_fn.png" alt="" width="87" height="18" border="0" /></a></div>
Ne vous laissez pas impressionner par la notation. <strong><em>f</em></strong> est le petit nom donné à la fonction, la lettre <strong>x</strong> à gauche de la flèche désigne l’argument de la fonction et à droite se trouve sa définition. En donnant une valeur spécifique au paramètre, par exemple <strong>1</strong> nous obtenons :
<div class="separator" style="clear: both; text-align: center;"><a href="http://3.bp.blogspot.com/-TIHMpxooJEs/UNDuY-ARvOI/AAAAAAAAAyo/3hYT7uz3Nh0/s1600/f_double_fn_1.png"><img src="http://3.bp.blogspot.com/-TIHMpxooJEs/UNDuY-ARvOI/AAAAAAAAAyo/3hYT7uz3Nh0/s400/f_double_fn_1.png" alt="" width="120" height="19" border="0" /></a></div>
Si x vaut <strong>3</strong> la valeur retournée par la fonction est :
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-bO7dvC0OMWQ/UNDvY49TArI/AAAAAAAAAy4/KOChqy8kLQg/s1600/f_double_fn_3.png"><img src="http://1.bp.blogspot.com/-bO7dvC0OMWQ/UNDvY49TArI/AAAAAAAAAy4/KOChqy8kLQg/s400/f_double_fn_3.png" alt="" width="124" height="19" border="0" /></a></div>
Au vu de ces valeurs il est aisé de comprendre que cette fonction double la valeur qu’on lui passe en paramètre. Renommons la fonction afin de mieux exprimer cette intention :
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-xF14ET1FYyI/UNDvoSthtHI/AAAAAAAAAzI/6PCyyFsBs30/s1600/double_fn.png"><img src="http://1.bp.blogspot.com/-xF14ET1FYyI/UNDvoSthtHI/AAAAAAAAAzI/6PCyyFsBs30/s400/double_fn.png" alt="" width="122" height="14" border="0" /></a></div>
Le langage Scala est utilisé dans la suite pour illustrer nos propos. Et la fonction double est codée en Scala comme suit :

[scala]
def double(number: Int) = 2 * number
[/scala]

Dans le code ci-dessus le mot clé <strong>def</strong> permet de définir une fonction. Il est suivi du nom de la fonction. J’ai choisi, à la place du paramètre <strong>x</strong>, un nom plus parlant : <strong>number</strong>. L’annotation <strong>Int</strong> (pour Integer) spécifie au compilateur que le paramètre <strong>number</strong> est un entier. Le reste du code est une traduction parfaite du pseudo code.

Les sorties suivantes de la console interactive (REPL) de Scala permettent de tester notre fonction :

[scala]
$ scala
scala&amp;gt; def double(number: Int) = 2 * number
double: (number: Int)Int
scala&amp;gt; double(3)
res0: Int = 6
scala&amp;gt; double(1)
res1: Int = 2
[/scala]

Une fonction peut avoir plusieurs arguments par exemple la fonction faisant la somme de deux nombres entiers prend deux arguments. Soient <strong>x</strong> et <strong>y</strong> les deux entiers dont nous souhaitons faire la somme, la fonction <strong>add</strong> est définie comme suit :
<div class="separator" style="clear: both; text-align: center;"><a href="http://1.bp.blogspot.com/-C1FzDY8ZYYc/UND5HbuFCuI/AAAAAAAAAzc/eR-SMu9lb1g/s1600/add_fn.png"><img src="http://1.bp.blogspot.com/-C1FzDY8ZYYc/UND5HbuFCuI/AAAAAAAAAzc/eR-SMu9lb1g/s400/add_fn.png" alt="" width="145" height="19" border="0" /></a></div>
Cette définition se traduit aisément en Scala de la façon suivante :

[scala]
def add(x:Int, y:Int) = x + y
[/scala]

Le test de la fonction donne :

[scala]
$ scala
scala&amp;gt; def add(x:Int, y:Int) = x + y
add: (x: Int, y: Int)Int
scala&amp;gt; add(1, 2)
res0: Int = 3
[/scala]

Et si nous souhaitions définir une fonction qui incrémente un entier nous pouvons réutiliser la fonction <strong>add</strong>. En effet incrémenter un entier revient à lui ajouter <strong>1</strong> :
<pre>def increment(number: Int) = add(number, 1)</pre>
En action cela donne:

[scala]
scala&amp;gt; def increment(number: Int) = add(number, 1)
increment: (number: Int)Int
scala&amp;gt; increment(3)
res1: Int = 4
scala&amp;gt; increment(10)
res2: Int = 11
[/scala]
<p class="note"><strong>Note</strong> : Une propriété importante de chacune des fonctions que nous avons vues jusque là est le fait que la valeur de retour est entièrement déterminée par les arguments de la fonction.</p>
Munis de ces informations passions aux fonctions d’ordre supérieur.
<h2>Les fonctions d’ordre supérieur</h2>
Une fonction d’ordre supérieur est une fonction qui a au moins l’une des deux caractéristiques suivantes :
<ul>
	<li>prend en paramètre une ou plusieurs fonctions</li>
	<li>sa valeur de retour est une fonction</li>
</ul>
Les fonctions d’ordre supérieur aident à l’expressivité du code en mettant l’attention sur la tâche à accomplir plutôt que sur la façon de l’accomplir : le code dévient ainsi plus déclaratif. Cela devient plus clair avec un exemple. Considérons la classe Person ci-dessous :

[java]
public class Person {
 private final String name;
 private final String email;
 private final int age;

 public Person(String name, String email, int age) {
  this.name = name;
  this.email = email;
  this.age = age;
 }

 public String getName() {
  return name;
 }

 public String getEmail() {
  return email;
 }

 public int getAge() {
  return age;
 }
}
[/java]

Supposons que vous disposez d’une liste d’objets de type <strong>Person</strong> sur laquelle vous souhaitez faire différentes opérations : transformation, filtrage etc. Commençons par la transformation de la liste.
<h2>Transformation des éléments d’une collection</h2>
Voici la liste initiale :

[java]
List&amp;lt;Person&amp;gt; persons = Arrays.asList(//
				new Person(&amp;quot;Toto&amp;quot;, &amp;quot;toto@email.com&amp;quot;, 23), //
				new Person(&amp;quot;John Doe&amp;quot;, &amp;quot;john.doe@email.com&amp;quot;, 34), //
				new Person(&amp;quot;Mad Max&amp;quot;, &amp;quot;mad.max@crazymail.com&amp;quot;, 20), //
				new Person(&amp;quot;Jane Doe&amp;quot;, &amp;quot;jane@email.com&amp;quot;, 17));
[/java]

La première transformation qui nous intéresse est l’extraction des adresses email à partir de la liste des personnes. C’est ce que fait la méthode <strong>extractEmails</strong> :

[java]
public static List&amp;lt;String&amp;gt; extractEmails(List&amp;lt;Person&amp;gt; persons) {
	List&amp;lt;String&amp;gt; emails = new ArrayList&amp;lt;String&amp;gt;();

	for (Person person : persons) {
		emails.add(person.getEmail());
	}

	return emails;
}
[/java]

Testons ce que cela donne :

[java]
System.out.println(&amp;quot;Emails = &amp;quot; + extractEmails(persons));
[/java]


Sortie de la console :

[bash]
Emails = [toto@email.com, john.doe@email.com, mad.max@crazymail.com, jane@email.com]
[/bash]

On obtient une liste ayant la même taille que la liste des personnes mais ne contenant que les adresses email. Le code de la méthode <strong>extractEmails</strong> recèle quelques défauts dont la “<strong><em>dilution</em></strong>” de l’intention de la méthode dans du code verbeux.

Avant de voir comment résoudre ces défauts implémentons une autre fonctionnalité : extraire les noms à partir de la liste des personnes :

[java]
public static List&amp;lt;String&amp;gt; extractNames(List&amp;lt;Person&amp;gt; persons) {
	List&amp;lt;String&amp;gt; names = new ArrayList&amp;lt;String&amp;gt;();

	for (Person person : persons) {
		names.add(person.getName());
	}

	return names;
}
[/java]

Cette méthode fait ressortir un autre défaut de notre code : la <strong><em>duplication</em></strong>. Les deux méthodes ne diffèrent véritablement que par la transformation effectuée à chaque itération sur l’élément de la liste initiale. Cette transformation peut être matérialisée par une fonction <strong><em>f</em></strong> qui prend comme argument un objet de type Person et retourne son nom ou son adresse email. Ainsi l’implémentation de l’une ou l’autre de nos fonctions se résume comme suit : "<em>applique la fonction <strong><em>f</em></strong> à chaque élément de la liste initiale et renvoie-moi la liste correspondante</em>."

Nous pouvons schématiser cette phrase de la façon suivante, avec à gauche la liste initiale et à droite la liste obtenue suite à la transformation :
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-HaOuoJVF4MM/UNIyFI0DaKI/AAAAAAAAAzw/HQkRqlUkB3U/s1600/map.png"><img src="http://4.bp.blogspot.com/-HaOuoJVF4MM/UNIyFI0DaKI/AAAAAAAAAzw/HQkRqlUkB3U/s400/map.png" alt="" width="293" height="26" border="0" /></a></div>
Cela correspond exactement à ce que fait la fonction <strong><em>map</em></strong>. La figure ci-dessous donne le fonctionnement de la méthode <strong><em>map</em></strong> présente sur la classe <strong>List</strong> de Scala.
<div class="separator" style="clear: both; text-align: center;"><a href="http://4.bp.blogspot.com/-ejBbXTsqL4Y/UNJEvkJRmJI/AAAAAAAAA0E/UwL2jaSK_m8/s1600/map_illustration.jpg"><img src="http://4.bp.blogspot.com/-ejBbXTsqL4Y/UNJEvkJRmJI/AAAAAAAAA0E/UwL2jaSK_m8/s400/map_illustration.jpg" alt="" width="400" height="300" border="0" /></a></div>
Comme mentionné sur la figure la fonction map prend en paramètre une fonction, celle que nous souhaitons appliquer à chaque élément de la liste initiale. Dans notre cas la fonction a comme type <strong><em>Person =&gt; String</em></strong>.

Nous allons maintenant réimplémenter nos deux méthodes en utilisant la méthode map disponible dans l'<a href="http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List">API des listes Scala</a>.
<h2>Les fonctions extractEmails et extractNames revisitées</h2>
Définition de la classe <strong>Person</strong> :

[scala]
case class Person(name: String, email: String, age: Int)
[/scala]

Nous définissons d’abord la fonction qui extrait une adresse email à partir d’une instance de Person :

[scala]
def extractEmail(person: Person) = person.email
[/scala]

Puis nous passons cette fonction à la méthode map de la liste afin de récupérer la liste des adresses email.

[scala]
def extractEmails(persons: List[Person]): List[String] = persons.map(extractEmail)
[/scala]

Sur le même principe voici la définition de la fonction <strong>extractNames</strong> :

[scala]
def extractName(person: Person) = person.name
def extractNames(persons: List[Person]): List[String] = persons.map(extractName)
[/scala]
<div><strong>Note</strong> : Grâce aux fonctions anonymes nous pouvons nous passer des fonctions <strong>extractEmail</strong> et <strong>extractName</strong> qui n’ont pas grand intérêt.</div>
[scala]
def extractNames(persons: List[Person]): List[String] = persons.map(p =&amp;gt; p.name)
def extractEmails(persons: List[Person]): List[String] = persons.map(p =&amp;gt; p.email)
[/scala]

Qu'avons-nous gagné par rapport à avant ? Le code est plus expressif en éliminant le bruit et en mettant l’accent sur l’intention du code : "<em>mapper chaque élément de la liste sur son application à la fonction que nous passons à la méthode</em> <strong><em>map</em></strong>".
Les fonctions d’ordre supérieur rendent pratique une autre opération courante sur les collections : le filtrage.
<h2>Filtrer une collection</h2>
Nous disposons d’une liste de nombres entiers allant de <strong>1</strong> à <strong>15</strong> et nous souhaitons filtrer cette liste en éliminant tous les éléments qui ne sont pas des multiples de 3. Ce genre d’opération est résolu en programmation fonctionnelle avec la fonction d’ordre supérieur filter. Elle prend en arguments une liste et un <strong>prédicat</strong>, une fonction qui retourne "vrai" ou "faux". Dans notre exemple le prédicat retourne “vrai” si son argument est un multiple de 3 et "faux" dans le cas contraire. Scala étant un langage orienté-objet <strong><em>filter</em></strong> est une méthode de <strong><a href="http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.List"><em>List</em></a></strong>.
Voici la définition du prédicat :

[scala]
scala&amp;gt; def isMultipleOfThree(number: Int) = number % 3 == 0
isMultipleOfThree: (number: Int)Boolean
scala&amp;gt; isMultipleOfThree(2)
res0: Boolean = false
scala&amp;gt; isMultipleOfThree(6)
res1: Boolean = true
[/scala]

Et la liste de nombres :

[scala]
scala&amp;gt; val numbers = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15)
numbers: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15)
[/scala]

Le filtrage s’effectue simplement en passant la fonction <strong><em>isMultipleOfThree</em></strong> à la méthode <strong><em>filter</em></strong> de la liste de nombres :

[scala]
scala&amp;gt; val multiplesOfThree = numbers.filter(isMultipleOfThree)
multiplesOfThree: List[Int] = List(3, 6, 9, 12, 15)
[/scala]

Ceci peut être réécrit en remplaçant <strong>isMultipleOfThree</strong> par une fonction anonyme :

[scala]
scala&amp;gt; numbers.filter(number =&amp;gt; number % 3 == 0)
res2: List[Int] = List(3, 6, 9, 12, 15)
[/scala]

Nous allons maintenant combiner <strong><em>filter</em></strong> et <strong><em>map</em></strong>.
<h2>Combiner filter et map</h2>
Nous souhaitons obtenir la somme des doubles des multiples de <strong>3</strong> compris entre <strong>1</strong> et <strong>15</strong>. Cela correspond à une opération de filtrage (suppression des nombres non multiples de <strong>3</strong>) suivie d’une transformation des éléments (doublement des éléments des éléments restants) et enfin une opération de réduction (la sommation). On y arrive aisément avec le code suivant :

[scala]
scala&amp;gt; val numbers = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15)
numbers: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15)
scala&amp;gt; numbers.filter(number =&amp;gt; number % 3 == 0).map(number =&amp;gt; number * 2).sum
res7: Int = 90
[/scala]

Il nous reste une dernière fonction d’ordre supérieur à découvrir avant de conclure cet article : la fonction <strong><em>fold</em></strong>.
<h2>La fonction fold, le couteau suisse</h2>
La fonction <strong>fold</strong> est un véritable couteau suisse. Elle réduit les éléments d’une liste en une valeur. Cette fonction existe en deux versions : l’une (<strong><em>foldLeft</em></strong>) parcourt la liste de la gauche vers la droite et l’autre de la droite vers la gauche (<strong><em>foldRight</em></strong>). Nous allons plutôt utiliser la première dont voici la signature :

[scala]
def foldLeft[B](z: B)(f: (B, A) ⇒ B): B
[/scala]

C’est une fonction <a href="http://fr.wikipedia.org/wiki/Curryfication">curryfiée</a>. Elle prend une valeur initiale de type <strong>B</strong>, désignée par <strong><em>z</em></strong> et une fonction <strong><em>f</em></strong> prenant deux arguments de types <strong>A</strong> (le type des éléments de la liste) et B. Pour chaque élément de la liste la fonction <strong><em>f</em></strong> est invoquée. <strong><em>f</em></strong> est invoquée avec la valeur initiale z fournie à la méthode <strong><em>foldLeft</em></strong>. Puis la valeur résultante est passée avec le deuxième élément de la liste à la fonction <strong><em>f</em></strong>. Ce processus continue avec tous les éléments de la liste. Pensez à lire <a href="http://www.arolla.fr/blog/2011/10/listes-scala-methodes-foldleft-et-foldright/">cet article</a> si vous souhaitez en savoir davantage. Place à l’action !

Afin de comprendre comment fonctionne <strong><em>foldLeft</em></strong> nous allons résoudre trois petits problèmes. Le premier problème consiste à faire la somme des éléments d’une liste d’entiers.
La valeur initiale dans le cas de la somme est <strong>0</strong> (l’élément neutre de l’addition) et la fonction <strong><em>f</em></strong> additionne ces deux paramètres. Voici ce que cela donne dans le REPL :

[scala]
scala&amp;gt; val nums = List(1, 2, 3, 4)
nums: List[Int] = List(1, 2, 3, 4)
scala&amp;gt; val sum = nums.foldLeft(0)((acc, num) =&amp;gt; acc + num)
sum: Int = 10
scala&amp;gt; val nums2 = List(1, 3, 5, 7)
nums2: List[Int] = List(1, 3, 5, 7)
scala&amp;gt; val sum2 = nums2.foldLeft(0)((acc, num) =&amp;gt; acc + num)
sum2: Int = 16
[/scala]

Simple non ?! Ne nous arrêtons pas en si bon chemin continuons sur le deuxième problème : faire le produit des éléments d'une liste. La valeur initiale passée à foldLeft dans ce cas est l’élément neutre de la multiplication à savoir <strong>1</strong>. Quant à la fonction à passer foldLeft elle fait le produit de ces deux paramètres.

[scala]
scala&amp;gt; val nums = List(1, 2, 3, 4)
nums: List[Int] = List(1, 2, 3, 4)
scala&amp;gt; val prod = nums.foldLeft(1)((acc, num) =&amp;gt; acc * num)
prod: Int = 24
scala&amp;gt; val nums2 = List(1, 3, 5, 7)
nums2: List[Int] = List(1, 3, 5, 7)
scala&amp;gt; val prod2 = nums2.foldLeft(1)((acc, num) =&amp;gt; acc * num)
prod2: Int = 105
[/scala]

So far so good! Maintenant résolvons le dernier exercice pour la route. Il s’agit de construire à partir d’une liste d’entiers une map dont les clés sont les éléments pairs de la liste et les valeurs leurs doubles. Voyons un exemple afin de clarifier les choses :
<pre>List(7, 2, 10, 8, 6) -&gt; Map(2 -&gt; 4, 10 -&gt; 20, 6 -&gt; 12)</pre>
La valeur initiale est une map vide. La fonction passée à <strong><em>foldLeft</em></strong> prend une map et un entier. Si l’entier est pair son double est mappé sur sa valeur. La fonction retourne la map résultante.

[scala]
scala&amp;gt; val nums = List(1, 2, 3, 4)
nums: List[Int] = List(1, 2, 3, 4)

scala&amp;gt; val evenDoubled = nums.foldLeft(Map[Int, Int]()) { (acc, num) =&amp;gt;
| if (num % 2 == 0) acc + ((num, 2 * num)) else acc
| }
evenDoubled: scala.collection.immutable.Map[Int,Int] = Map(2 -&amp;gt; 4, 4 -&amp;gt; 8)
[/scala]
<h2>Pour conclure</h2>
Nous arrivons à la fin de cette introduction aux fonctions d’ordre supérieur. Elles sont particulièrement adaptées aux traitements sur des collections. Mais leur usage va au-delà des collections. Consultez la liste de références ci-dessous pour aller loin.
<h2>Références</h2>
<ul>
	<li><a href="http://en.wikipedia.org/wiki/Higher-order_function">Higher Order functions</a></li>
	<li><a href="http://learnyouahaskell.com/higher-order-functions">Learn you a Haskell for Great Good !</a></li>
	<li>Pure, functional Javascript</li>
</ul>
