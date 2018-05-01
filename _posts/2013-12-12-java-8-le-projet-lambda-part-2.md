---
layout: post
title: "Java 8 : Le projet Lambda (part 2)"
date: 2013-12-12 12:38
author: yakhya dabo
comments: true
categories: [Actu, java, Java 8, java8, jdk, jdk8, lambda, Programmation]
---
<h2>Introduction</h2>
<p style="text-align: justify;">Dans <span style="color: #0000ff;"><strong><a href="http://www.arolla.fr/blog/2013/06/java-8-le-projet-lambda-part-1/"><span style="color: #0000ff;">le précédent billet</span></a></strong></span>, nous avons introduit les objectifs du projet Lambda [<i>sur le court terme il s’agit de simplifier les itérations internes pour la manipulation des collections et sur le long terme d’inté</i><i>grer da</i><i>ns Java le style de programmation fonctionnelle</i>]. La plupart des changements portent sur l'interface <b>Collection</b> qui n'a pas connu une grande évolution depuis son apparition dans le <b>JDK 1.2</b>. On traitera dans cette partie, avec un peu plus de détails, des concepts clés ajoutés à la plateforme pour faciliter l'intégration des Lambda expressions. Il s'agit :</p>

<ul>
	<li style="text-align: justify;">des VEM (Virtual Extension Method)</li>
	<li style="text-align: justify;">de l'Interface Stream</li>
	<li style="text-align: justify;">des Interface Fonctionnelles</li>
	<li style="text-align: justify;">des Méthodes de références</li>
	<li style="text-align: justify;">de l'Inférence de type</li>
</ul>
<h2><b>Les VEM (Virtual Extension Method)</b></h2>
<p style="text-align: justify;">L’interface <b>Collection</b>, qui est l’une des plus concernées par le projet, va être retouchée pour prendre en compte les nouvelles opérations. Actuellement plusieurs librairies comme Hibernate offrent déjà des implémentations de cette interface. La grande limite avec les interfaces est qu’une fois implémentées elles deviennent réfractaires à l’évolution; modifier une interface revient à casser ses différentes implémentations. La question est donc d’ajouter de nouvelles méthodes aux interfaces existantes sans toucher aux implémentations.</p>
Pour cela plusieurs solutions ont été étudiées :
<ul>
	<li style="text-align: justify;">Ajouter des méthodes statiques à la classe <b>Collections</b> (limite : ne respecte pas vraiment le paradigme Orienté Objet).</li>
	<li style="text-align: justify;">Ajouter de nouvelles interfaces dédiées aux Lambda Exp, par exemple créer une interface <b>Collection 2</b>(inconvénient : aucun apport pour les programmes séquentiels).</li>
	<li style="text-align: justify;">Ajouter les nouveaux opérateurs (<b><i>map</i></b>, <b><i>filter</i></b>, <b><i>fold</i></b>) aux implémentations des collections (inconvénient : mauvaise pratiques de la POO, entraîne des usages abusifs de l’opérateur <b>cast</b>, …).</li>
</ul>
Finalement c’est vers les méthodes virtuelles d’extensions que le choix a été porté.
<p style="text-align: justify;">L’objectif des méthodes virtuelles, anciennement connues sous le nom de <b><i>« public defender methods »</i></b>, est d’assurer la compatibilité ascendante en faisant évoluer une interface sans casser ses différentes implémentations. Le principe est de fournir à une interface l’implémentation par défaut d’une de ses méthodes, et cette dernière sera utilisée si aucune implémentation n’est définie par la classe qui implémente l’interface.</p>
<b>Exemple :</b>

[java]
	public interface A{

		default void foo(){

		}

	}

	public class B implements A {

	}
[/java]
[java]
	A a = new B();

	a.foo();
[/java]

<b>B</b> hérite implicitement de la méthode <b><i>foo()</i></b> (tout comme des méthodes <b><i>equals()</i></b>, <b><i>toString()</i></b>, <b><i>hashCode()</i></b>…) .

<b>Exemple avec List :</b>
On veut étendre la méthode <b><i>sort()</i></b> de l’interface <b>List</b>.

[java]
	interface List {
		// ...
		default void sort(Comparator&lt;? super T&gt; cmp) {
			Collections.sort(this, cmp);
		}
	}
[/java]
<p style="text-align: justify;"><b>Le problème du diamant</b></p>
<p style="text-align: justify;">La notion de VEM rend ainsi possible ce qui était jusque-là impossible en Java, à savoir  l’héritage multiple – rien n’empêche d’implémenter deux interfaces qui fournissent des méthodes virtuelles – et amène en même temps avec elle un problème commun à résoudre, <b><i>« The diamond problem »</i></b>. Que faire quand une classe hérite de deux interfaces qui ont des méthodes virtuelles identiques.</p>

<h6 style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/11/Image1.gif"><img class=" wp-image-2047 aligncenter" alt="Image1" src="http://www.arolla.fr/blog/wp-content/uploads/2013/11/Image1.gif" width="125" height="188" /></a>problème du diamant</h6>
[java]
	interface A {
		default void foo() {
			// …
		}
	}

	interface B extends A {

	}

	interface C extends A {

	}

	class D implements B, C {

	}
[/java]
<p style="text-align: justify;">Quand le Classloader charge <b>D</b> il vérifie si toutes les méthodes des interfaces <b>B</b> et <b>C</b> sont implémentées, si c’est le cas aucune méthode virtuelle ne sera utilisée, sinon le Classloader essaye de compléter avec les méthodes virtuelles des interfaces <b>B</b> et <b>C</b>. En cas d’ambiguïté (deux interfaces avec la même vem) une exception est levée.</p>
Pour résoudre ce problème du diamant on invoque explicitement la méthode virtuelle à hériter.

[java]
	class D implements B, C {

		void foo() {

			A.super.foo() // ou B.super.foo()

		}

	}
[/java]
<p style="text-align: justify;">En dehors de ce problème du diamant, l’introduction de la notion de VEM n’a pas été un choix unanimement apprécié, notamment sur le fait qu’il casse la notion de « contrat » que pouvait représenter une interface entre un fournisseur et un client.</p>

<h2><b>L’interface Stream</b></h2>
<p style="text-align: justify;">Il y a <b>Linq</b> pour <b>.Net</b>, <b>Collections API</b> pour <b>Scala</b> et <b>Stream</b> pour <b>Java</b>. C’est la nouvelle interface qui va prendre en charge le style fonctionnel de l’interface Collection, <b><i>map()</i></b>, <b><i>filter()</i></b>, <b><i>reduce()</i></b>, … Son choix permet de garantir la compatibilité ascendante. Au début il était prévu d’ajouter les fonctions map, filter et reduce à l’interface Collection comme des VEM, mais rien ne garantit qu’il n’existe pas déjà des implémentations de Collection, avec des méthodes map, filter, reduce de mêmes signatures.</p>
<p style="text-align: justify;">Ce choix permet aussi de garder une certaine cohérence : faire une séparation entre les anciennes méthodes de l’interface Collection et les nouvelles qui présentent un style fonctionnel.</p>
<p style="text-align: justify;">En langage fonctionnel, le <strong>Stream</strong> est défini comme une séquence de valeurs. Il se trouve dans le package, <b>java.lang.stream</b> avec les opérateurs primitifs (<b>IntStream</b>, <b>LongStream</b>, <b>DoubleStream</b>). Dans <b>JDK 8</b> le Stream joue le même rôle que l’interface <b>Iterator</b>, il permet d’accéder aux données d’une Collection, à la différence qu’il ne fournit aucun mécanisme de stockage, il offre juste un moyen de créer des séquences de données (par exemple les entiers premiers, les nombres pairs, éléments d’une collection satisfaisant un critère, …) Les données ne sont évaluées que quand le programme en a vraiment besoin (<b><i>lazy evaluation</i></b>).</p>

[java]
	public interface Stream {
		Stream 	filter(Predicate&lt;? super T&gt; predicate) ;
		 Stream  map(Function&lt;? super T,? extends R&gt; mapper) ;
		Optional 	reduce(BinaryOperator accumulator) ;
		Optional 	min(Comparator&lt;? super T&gt; comparator) ;
		Optional 	max(Comparator&lt;? super T&gt; comparator) ;
		long count() ;
		...
	}
[/java]

L’évaluation lazy a deux avantages, il permet de :
<ul>
	<li style="text-align: justify;">représenter un ensemble infini</li>
	<li style="text-align: justify;">paralléliser des opérations</li>
</ul>
<p style="text-align: justify;">L’interface <b>Collection</b> est étendue avec la méthode <b><i>stream()</i></b>. Et toute la magie des pipelines se trouve à ce niveau. Sur le <b>Stream</b> retourné on peut enchainer plusieurs appels des fonctions <b><i>filter()</i></b>, <b><i>map()</i></b>, <b><i>forEach()</i></b>, … et terminer par un <b><i>count()</i></b>, <b><i>min()</i></b>, <b><i>max()</i></b>, …. Comme nous l’avons évoqué dans la première partie, pour passer d’un traitement séquentiel à un traitement « parallèle » de <b><i>stream()</i></b> ce ne sera qu’une question de syntaxe, <b><i>parallelStream()</i></b>.</p>

[java]
	interface Collection {
		// …
		// Returns a sequential Stream with this collection as its source
		default Stream stream() {
			return StreamSupport.stream(spliterator(), false);
		}
		//Returns a possibly parallel Stream with this collection as its source.
		default Stream parallelStream() {
			return StreamSupport.stream(spliterator(), false);
		}
	}

[/java]

Exemple :

[java]
	people.stream()
					.filter(Person p -&gt; p.getAge() &gt;= MAX_AGE))
					.forEach(Person p -&gt; p.setSalary(p.getSalary() * x))
					.mapToInt( p -&gt; p.getSalary())
					.reduce(0, (x,y) -&gt; x+y);
[/java]
<h2><b>Functional Interface</b></h2>
<p style="text-align: justify;">L’Interface Fonctionnelle fait partie des grandes nouveautés introduites dans la nouvelle version de la plateforme. L’objectif est de typer les expressions lambda.</p>
<p style="text-align: justify;">Dans les exemples fournis dans la première partie de la série on a remplacé une classe anonyme par des lambda expressions pour simplifier la manipulation des collections. Ces classes anonymes qui ont la particularité de n’avoir qu’une seule méthode sont souvent passées en paramètre à des méthodes. C’est le cas pour les interfaces <b>Runnable</b>, <b>Comparator</b>, <b>Callable</b>, … Jusque-là connues sous le nom de <b>SAM (Simple Abstract Method)</b>, elles seront les <b>«Functionnal Interface » </b>de Java 8.</p>
<b>Exemple :</b>

[java]
	List employees = new ArrayList()&lt;&gt;;

	employees.filter(new Predicate() {
		public boolean test(Employee emp) {
			return emp.age &gt;= 45;
		}
	});
[/java]

remplacé par :

[java]
employees.stream().filter (emp -&gt; emp.age &gt;= 45);
[/java]

On dispose déjà, depuis les précédentes versions, dans le package <b>java.lang</b> des interfaces fonctionnelles suivantes :

[java]
public interface Runnable { void run(); }

public interface Callable { V call() throws Exception; }

public interface ActionListener { void actionPerformed(ActionEvent e); }

public interface Comparator { int compare(T o1, T o2); boolean equals(Object obj); }
[/java]
<p style="text-align: justify;">Le langage s’enrichit également d’un nouveau package <b>java.util.function</b> qui vient avec les «Functionnal Interface » de base, <b>Consumer</b>, <b>Mapper</b>, <b>Predicate</b>, ….</p>
<b>1. Predicate</b> : Effectue un test sur l’objet en paramètre et retourne un booléen.

[java]
	public interface Predicate {
		boolean test(T t);
	}
[/java]

Exemple d’utilisation :

[java]
	List employees = new ArrayList&lt;&gt;();

	employees.filter(new Predicate() {
		public boolean test(Employee emp) {
			return emp.age &gt;= 45;
		}
	});
[/java]

Version lambda :

[java]
employees.stream().filter (emp -&gt; emp.age &gt;= 45);
[/java]

<b>2. Consumer : </b> Effectue une action sur l’objet en paramètre.

[java]
	public interface Consumer {
		void accept(T t);
	}
[/java]

Exemple d’utilisation :

[java]
	employees.forEach(new Consumer() {
		public void accept(Employee emp) {
			System.out.println(emp);
		}
	});
[/java]

Version lambda :

[java]
employees.stream().forEach (emp -&gt; System.out.println(emp) );
[/java]

<b>3. Mapper :</b> Applique une fonction sur un objet <b>T</b> et retourne un objet de type <b>R</b>.

[java]
	public interface Mapper&lt;T,R&gt; {
		R map(T t);
	}
[/java]

Exemple d’utilisation :

[java]
	List employees = new ArrayList&lt;&gt;();

	List ages = employees.map(new Mapper() {
		public String map(Employee emp) {
			return emp.age;
		}
	});
[/java]

Version lamba :

[java]
List = employees.stream().map(emp -&gt; emp.age)
[/java]

<b>L’Annotation @FuntionlInterface</b>
<p style="text-align: justify;">En Java pour vérifier qu’une méthode a été « overridée » on peut utiliser l’annotation <b>@Override</b>. L’annotation <b>@FuntionlInterface</b> joue le même rôle dans Java 8, elle permet de déterminer qu’une interface peut être inter changée avec une expression lambda, autrement dit elle est fonctionnelle. Son utilisation n’est toutefois pas obligatoire.</p>

<h2><b>L’inférence de type</b></h2>
<p style="text-align: justify;">L’inférence de type, l’une des principales caractéristiques de la programmation fonctionnelle, permet de déterminer dynamiquement le type d’une expression. En effet le type d’une expression lambda est une Interface Fonctionnelle. A la compilation une expression lambda est remplacée par une implémentation de son interface fonctionnelle. Or dans la définition d’une expression lambda l’interface fonctionnelle de l’expression n’intervient pas, le type n’est pas précisé. De ce fait, d’un contexte à un autre, l’expression <b><i>x -&gt; 2 * x </i></b>peut correspondre à différentes instances d’interfaces fonctionnelles.</p>
Dans cet exemple :

[java]
	public interface IntOperation {
		int operate(int i);
	}

	public interface DoubleOperation {
		double operate(double i);
	}
[/java]

Les deux écritures suivantes sont toutes correctes :

[java]
DoubleOperation do = x -&gt; x * 2;
[/java]
[java]
IntOperation io = x -&gt; x * 2;
[/java]
<p style="text-align: justify;">Dans le premier exemple l’expression lambda a pour type l’interface <b>DoubleOperation</b>, dans le second l’interface <b>IntOperation</b>. Pour qu’une interface soit candidate il suffit qu’elle soit une Interface Fonctionnelle et ait la même signature que l’expression lambda. Le compilateur utilise le mécanisme d’<b><i>inférence de type</i></b> pour déterminer dynamiquement la bonne interface fonctionnelle en fonction du contexte dans lequel l’expression est utilisée. Le contexte peut être représenté par la déclaration, la signature de la méthode, le type de retour, l’affectation ou le cast. Pour chacun de ces cas on a un type d’inférence correspondant.</p>
<b>Inférence par déclaration :</b> Le type est déterminé à la déclaration

[java]
	public interface IntOperation {
		int operate(int i);
	}

	public interface DoubleOperation {
		double operate(double i);
	}

	DoubleOperation doubleOp = x -&gt; x * 2;

	IntOperation intOp = x -&gt; x * 2;
[/java]

<b>Inférence par Affectation :</b> Le type est déterminé à partir du type de la variable de gauche.

[java]
	DoubleOperation doubleOp  ;
	doubleOp  = x -&gt; x * 2;

	IntOperation intOp ;
	intOp = x -&gt; x * 2;
[/java]

<b>Inférence par Arguments de method ou Constructeur :</b>

[java]
	Int x =  new Thread (() -&gt; { System.out.println(&quot;Running in different thread&quot;);}
	).start();

[/java]
<p style="text-align: justify;">La classe <b>Thread</b> a plusieurs constructeurs, dont <b><i>Thread(Runnable target)</i></b> , le seul à avoir un argument comme paramètre. Cet argument <b>Runnable</b> étant une Interface Fonctionnelle, et ayant la même signature que l’expression lambda, il sera le type de l’expression <b>() -&gt; { System.out.println(« Running in different thread »);}</b>.</p>
<b>Inf par type de retour :</b> Le type de l’expression correspond au type de retour

[java]
	public Runnable toDoLater() {
		return () -&gt; System.out.println(&quot;later&quot;);;
	}
[/java]

<b>Inférence par Cast</b>
<p style="text-align: justify;">On l’utilise pour éviter des problèmes d’ambiguïté, dans des situations ou plusieurs interfaces fonctionnelles sont candidates.</p>

[java]
	public interface Runnable {
		void run();
	}

	public interface Callable {
		V call() throws Exception;
	}

	class TestCast{
		public static void invoke(Callable callable ){
			callable.call();
		}
		public static void invoke(Runnable runnable){
			runnable.run();
		}
	}

	TestCast.invoke(() -&gt; System.out.println(&quot;Done&quot;) );
[/java]

Ici le paramètre peut autant être un callable qu’un runnable. Pour lever l’ambiguïté on utilise l’opérateur <b>Cast</b>.

[java]
	Object runnable =  (Runnable) () -&gt; { System.out.println(&quot;Hello&quot;); };
	Object callable = (Callable) () -&gt;{ System.out.println(&quot;Done&quot;); };
[/java]
<h2><b>Les méthodes de références</b></h2>
<p style="text-align: justify;">Comme nous l’avons vu jusque-là, une lambda exp peut être considérée comme une implémentation de la méthode abstraite d’une classe anonyme. Les méthodes de références sont – elles aussi – une autre manière de fournir cette implémentation, toujours dans l’optique de gagner plus encore en simplicité en utilisant des méthodes déjà définies sur des classes ou des objets.</p>
La syntaxe est la suivante : <b>Class_name ::method</b>

<b>Exemple : </b>Pour trier une collection

[java]
public static void sort(T[] a, Comparator c);
[/java]
<p style="text-align: justify;">La méthode <b><i>sort()</i></b> prend en paramètre une collection et une interface fonctionnelle de type <b>Comparable</b>. Avec une classe anonyme on a le code suivant :</p>

[java]
List employees = new ArrayList()&lt;&gt;;
//...
Arrays.sort(employees, new Comparable () {
      @Override
      public int compareTo(Object o) {
        return 0;  
      }
    });
[/java]
<p style="text-align: justify;">On peut remplacer la classe anonyme (ou Interface Fonctionnelle) par une expression lambda compatible.</p>

[java]
Arrays.sort(employees, (Employee a, Employee b) -&gt; a.compareTo(b)));
[/java]

Ou tout simplement par une <b><i>« méthode de référence »</i></b> :

[java]
Arrays.sort(employees, Employee ::empCompareTo);
[/java]
<p style="text-align: justify;">On n’a pas besoin d’implémenter l’interface <b>Comparable</b> sur la classe <b>Employee</b>. Il suffit juste que la signature de la méthode <b><i>empCompareTo()</i></b> soit compatible avec la méthode <b><i>compareTo()</i></b> de l'interface <b>Comparable</b>. Si la classe a plusieurs méthodes qui portent le même nom, le choix de la bonne méthode se fera par <b>inférence de type</b>.</p>
On a quatre types de méthodes de référence :
<ul>
	<li>Référence sur une méthode statique : <strong>Object::toString</strong> équivaut à <strong>x -&gt; x.toString()</strong></li>
</ul>
<ul>
	<li>Référence sur une méthode d’objet : <strong>x::toString</strong> équivaut à <strong>() -&gt; x.toString()</strong></li>
</ul>
<ul>
	<li>Référence sur une méthode d'une instance d'un type particulier : <strong>String::valueOf</strong> équivaut à <strong>x -&gt; String.valueOf(x)</strong></li>
</ul>
<ul>
	<li>Référence sur un constructeur : <strong>ArrayList::new</strong> équivaut à<strong> () -&gt; new ArrayList&lt;&gt;()</strong></li>
</ul>
<h2><b>Conclusion</b></h2>
<p style="text-align: justify;">Dans cette série de deux articles nous avons présenté le projet Lambda, son objectif et les évolutions majeures qu’il apporte à la plateforme Java. Java 8 c’est donc le mariage entre le Fonctionnel et l’Orienté Objet pour faciliter la parallélisation dans les programmes et rendre le langage moins verbeux par l’utilisation de lambda expression à la place des classes anonymes. D’autres nouveautés seront également disponibles dans la nouvelle plateforme, notamment des améliorations sur les API Date et Time (http://openjdk.java.net/projects/jdk8/features).</p>
<p style="text-align: justify;">Sur le futur de Java, contrairement aux spéculations de ces dernières années qui prédisaient sa fin, tous les signaux sont verts pour les prochaines années. Il reste le langage le plus populaire avec une communauté très dynamique et le plus multiplateforme (Desktop, Server, Cloud et Mobile). Oracle a dépensé des centaines de millions de dollars pour apporter du sang neuf à Java et le mettre au même niveau que ses concurrents.</p>
A ceux qui restent encore dubitatifs je conseille une lecture de <strong><span style="color: #003366;"><a href=" http://www.drdobbs.com/jvm/if-java-is-dying-it-sure-looks-awfully-h/240162390"><span style="color: #003366;">l'avis</span></a></span></strong> de Andrew Binstock sur ce prétendu déclin.
<p style="text-align: justify;"><span style="color: #800000;"><em>But when it comes to Java being in some kind of long-term decline, I see little supporting evidence. The recent JavaOne show, that annual jamboree of Java coders, was clearly larger and better attended than it has been in either of the last two years. Vendors on the exhibiting floor with whom I spoke were unanimous (truly not a single exception) in saying that traffic, leads, and inquiries were up significantly over last year, which itself was better than the year before.
</em></span></p>

<h1><strong>Références</strong></h1>
State of the Lambda : http://cr.openjdk.java.net/~briangoetz/lambda/lambda-state-final.html

Lambda Faq : http://www.lambdafaq.org/

Java docs : http://download.java.net/jdk8/docs/
