---
layout: post
title: "Java 8 : Le projet Lambda (part 1)"
date: 2013-06-25 14:50
author: yakhya dabo
comments: true
categories: [Actu, java, Java 8, jdk8, lambda, Programmation]
---
<img class="alignleft" alt="http://www.kodcu.com/wp/wp-content/uploads/2012/12/java8.png" src="http://www.kodcu.com/wp/wp-content/uploads/2012/12/java8.png" width="184" height="183" />
<h2 style="text-align: justify;"><strong>Introduction</strong></h2>
<p style="text-align: justify;">Attendu impatiemment par près de neuf millions de développeurs comme la plus grande évolution de Java depuis l’introduction des « Generics » dans Java SE 5, le projet Lambda comporte deux volets. Sur le court terme il s’agit de simplifier les itérations internes pour la manipulation des collections et sur le long terme d’intégrer dans Java le style de programmation fonctionnelle.</p>
<p style="text-align: justify;">L’objectif principal du projet est, en s’appuyant sur la puissance de la programmation fonctionnelle, de rendre la construction de programmes parallèles aussi simple que possible, par l’introduction d’une nouvelle syntaxe appelée « <i>lambda expression</i> ». Cela va donner par la même occasion une syntaxe plus concise au langage Java. A cet effet, tous les composants de l’écosystème Java, le noyau du langage, la machine virtuelle et les librairies de base, sont appelés à évoluer.</p>
<p style="text-align: justify;">La sortie de la première release était prévue pour Septembre 2013 mais elle a été reportée pour Mars 2014 suite aux récents bugs de sécurité parut sur les navigateurs web:</p>
<p style="text-align: justify;"><i>The original schedule aimed to ship the release in early September 2013, but due to the recent focus on browser-related security issues that date is no longer achievable. The new schedule, proposed on 2013/4/18 and adopted on 2013/4/26, is as follows:</i></p>
<p style="text-align: justify;"><i>    </i><i>2012/04/26 M1       </i></p>
<p style="text-align: justify;"><i>    2012/06/14 M2</i></p>
<p style="text-align: justify;"><i>Lien : http://openjdk.java.net/projects/jdk8/</i></p>
<p style="text-align: justify;">Et tout ce retard fait sourire les développeurs de C#, Scala, Groovy, et même de C++, très familiers aux nouveaux concepts de Java, depuis quelques années.</p>
<p style="text-align: justify;">La promesse de la « comptabilité ascendante » qui fait la force et la faiblesse de Java est l’une des principales causes de la lenteur du projet. Ca a toujours été un défi dans Java, depuis ses débuts, d’assurer qu’un programme, une fois écrit,  puisse continuer à s’exécuter dans les nouvelles versions de la plateforme sans être retouché.</p>

<h2 style="text-align: justify;"><b>Petite précision du contexte</b></h2>
<p style="text-align: justify;">La loi de Moore se traduit aujourd’hui, non par la fréquence des processeurs, mais par la multiplication de leurs nombres. A l’ère des processeurs multi-cœur, exploiter le plus simplement possible la puissance de calcul des ordinateurs – par la parallélisation des processus - reste l’un des défis majeurs des langages de programmation.</p>
<p style="text-align: justify;">Brian Goetz, architecte Java chez Oracle et l’un des leaders du projet Lamba, donne dans les premières pages de son livre « Programmation concurrente en Java » des chiffres intéressants sur le nombre de bugs dévoilés sur des applications, conçues pour des architectures traditionnelles, lors de leur premier déploiement sur une architecture parallèle. En effet, même si nos programmes ne prennent pas en charge cette problématique de la parallélisation, on s’appuie régulièrement sur des librairies qui font du multithreading, surtout dans le développement web.</p>

<h2><b>Un exemple pour commencer</b></h2>
<i>Il s’agit de filtrer une collection de personnes agées.</i>

Le style le plus commun pour manipuler une collection Java est le suivant :

[java]
List oldPeople = … ;
List people = … ;

for (Person p : people) {
	if (p.age &gt;= Person.MAX_AGE)
		oldPeople.add(p);
}
[/java]
<p style="text-align: justify;">On itère manuellement sur la collection avec un type d’itérateur connu sous le nom d’itérateur externe. Il est simple à implémenter. Sa caractéristique est que le traitement (le Quoi) se fait de manière séquentielle (le Comment), il est laissé à la seule responsabilité de l’utilisateur de l’API Collection. On récupère un objet Person, on vérifie que son âge satisfait la condition pour le stocker dans une nouvelle liste.</p>
<p style="text-align: justify;">Si on doit paralléliser le traitement avec ce type d’itérateur Java offre plusieurs API dont les Executors. On aura donc le code suivant :</p>

[java]
public class Task implements Runnable {
	private List oldPeople;
	private Person person;

	public Task(List list, Person person){
		this.oldPeople = list;
		this.person = person;
	}

	@Override
	public void run() {
		if (person.getAge() &gt;= Person.MAX_AGE)
			oldPeople.add(person);
	}
}

public class ExecutorServiceTest {
	private List people = Collections.synchronizedList(new ArrayList());
	private List oldPeople = Collections.synchronizedList(new ArrayList());

	public ExecutorServiceTest() {
		people.add(new Person(&quot;Mr X&quot;,24));
		people.add(new Person(&quot;Mr Z&quot;,17));
		people.add(new Person(&quot;Miss A&quot;,45));
		people.add(new Person(&quot;M B&quot;,75));
	}

	public void run() {
		int nbOfProcessors = Runtime.getRuntime().availableProcessors();
		ExecutorService executorService = Executors.newFixedThreadPool(nbOfProcessors);
		for(Person person : people)
			executorService.execute(new Task(oldPeople,person));

			executorService.shutdown();
		while (!executorService.isTerminated()) {
		}
	}

	public static void main(String[] args) {
		new ExecutorServiceTest().run();
		System.exit(0);
	}
}
[/java]
<p style="text-align: justify;">Le premier constat qu’on peut faire ici se trouve au niveau de la différence syntaxique entre le premier programme (séquentiel) et le second programme (parallèle). Ils sont sensés effectuer le même « traitement » mais passer de l’un à l’autre n’est pas trivial. On est dans ce cas amené à prendre en compte le choix de paralléliser (ou non) depuis la phase de développement alors que pour la plupart des cas le problème n’apparait que plus tard.</p>
<p style="text-align: justify;">On voit ensuite que dans le second programme le code métier est complètement noyé dans du « bruit » et dispersé dans différentes classes. Cette complexité d’implémentation incite très peu  à utiliser ces API dans les programmes informatiques.</p>

<h2><b>L’Itérateur interne le sauveur ?</b></h2>
<p style="text-align: justify;">Avec les itérateurs internes on ne se contente que de spécifier le <b><i>traitement</i></b> à effectuer sur les objets (le <i>Quoi</i> sans le <i>Comment)</i>. Avec ce style la manière (séquentielle ou parallèle) peut être laissée à la discrétion de la collection. Un exemple avec la librairie Guava :</p>

[java]
Interface Predicate  {public boolean op(T t) ;}

oldPeople  = people.filter(new Predicate () {
	public boolean op(Person p) {
		return p.getAge() &gt;= Person.MAX_AGE;
	}
});
[/java]

Pour passer d’un style de programme à un autre on apporte une légère modification :

[java]
people.parallel().filter(new …);
people.sequential().filter(new …);
[/java]
<p style="text-align: justify;">Mais le problème ici est qu’on utilise une classe anonyme avec l’inconvénient de sa syntaxe inélégante qui s’étale sur plusieurs lignes (on parle de « problème vertical »), juste pour utiliser une seule méthode. On peut également rencontrer d’autres freins avec une classe anonyme :</p>
<p style="text-align: justify;">-          Dans une classe anonyme on ne peut pas accéder à une variable de la classe supérieure qui n’est pas déclarée avec <b><i>final.</i></b> (1). [Avec Java 8 le problème ne sera résolu que partiellement, on pourra accéder à une variable qui n’est pas déclarée avec <b><i>final</i></b>, mais sans pouvoir la modifier (effectivly final).]</p>
<p style="text-align: justify;">-          Le mot clé <b><i>This</i></b> ne fait pas référence à la classe supérieure, mais plutôt à la classe anonyme elle-même. Utiliser ClassName.This pour faire référence à la classe supérieure.</p>
<p style="text-align: justify;">-          Une variable de classe anonyme cache toute autre variable de la classe supérieure portant le même nom. (3)</p>
Exemple :

[java]
public void AnonymousSample() {

	String nonFinalVariable = &quot;Non Final Example&quot;; // (1)
	String variable = &quot;Outer Method Variable&quot;; // (3)

	new Thread(new Runnable() {

		String variable = &quot;Runnable Class Member&quot;; // (3)

		public void run() {
			String variable = &quot;Run Method Variable&quot;; // (3)
			System.out.println(&quot;-&amp;gt;&quot; + nonFinalVariable); // (1)
			System.out.println(&quot;-&amp;gt;&quot; + variable);
			System.out.println(&quot;-&amp;gt;&quot; + this.variable); // (2)
		}
	}).start();
}
[/java]
<p style="text-align: justify;">Dans les versions actuelles de Java, il est impossible de passer une méthode en paramètre. On passe toujours par un objet. Et c’est à niveau qu’on découvre le premier intérêt des « lambda expression », le remplacement des classes anonymes.</p>
Avec les « lambda expression » pour le même problème on aura une syntaxe plus concise et plus lisible sur une seule ligne. On n’a pas de variable locale intermédiaire comme dans les précédents exemples avec la variable <i>p</i>.

[java]
oldPeople = persons.filter(Person p -&gt; p.getAge() &gt;= MAX_AGE);
[/java]
<h2>Structure d'une lambda expression</h2>
<p style="text-align: justify;">Comme on peut le constater, une « lambda expression » est absolument équivalente à une classe anonyme, avec une structure différente. La syntaxe, identique à celle de Scala et C#,  est proche de celle d’une méthode (paramètre et corps de la méthode).</p>
<b>(argument_list) -&gt; function_body</b>

Quelques exemples de lambda expression :

[java]
(int x) -&gt; x + 1 ;
(int x, int y) -&gt; x + y ;
() -&gt;  System.out.println(&quot;I am a lambda expression&quot;);
[/java]

Sur plusieurs lignes on met des accolades :

[java]
(int x, int y) -&gt; { System.out.println(&quot; add : &quot; + (x+y));
System.out.println(&quot; mult : &quot; + (x*y)); }
[/java]
<p style="text-align: justify;">Les types des paramètres ne sont pas obligatoires, ils peuvent être obtenus par inférence, déduits en fonction du contexte (on reviendra plus en détail sur l’interférence dans la deuxième partie). Exemple :</p>

[java]
(x) -&gt; x + 1 ;
(x, y) -&gt; x + y ;
(x, y) -&gt; { System.out.println(&quot; Result : &quot; + (x+y)); }
[/java]
<p style="text-align: justify;">Une expression lambda peut être stockée dans une variable, passée en paramètre ou retournée comme résultat d’une méthode. Exemple:</p>

[java]
X = () -&gt; {while (true) { System.out.println(&quot;Hello&quot;); }}
new Thread(X).start();
[/java]
<h2>Structure pipeline (Pipe &amp; Filter)</h2>
<p style="text-align: justify;">Dans les systèmes Unix, le pattern Pipe &amp; Filter permet d’aligner une séquence de commandes, de sorte que la sortie d’une commande soit l’entrée de celle qui la suit. On retrouvera le même pattern dans la classe Collection de Java 8 avec le même principe, sous forme d’une structure pipeline. C’est une syntaxe qui permet de gagner en lisibilité et d’éviter de stocker des résultats intermédiaires dans des variables.</p>
En s’inspirant de l’exemple présenté par Brian Goetz dans le Java Magazine de <b><i>Septembre/Octobre 2012</i></b>, où l’on a à effectuer séquentiellement plusieurs traitements sur une même collection (filtrer par l’âge, multiplier les salaires par un coefficient x, afficher le résultat pour chaque personne, et faire ensuite le total) on peut réduire sur une seule instruction le code relatif à plusieurs traitements.

[java]
people.filter(Person p -&gt; p.getAge() &gt;= MAX_AGE))
				.forEach(Person p -&gt; p.setSalary(p.getSalary() * x))
				.map( p -&gt; p.getSalary())
				.forEach(s -&gt; System.out.println( s))
				.reduce(0, (x,y) -&gt; x+y);
[/java]
<p style="text-align: justify;">Si on veut que certaines parties des traitements soient effectuées en parallèle comme la multiplication du salaire par x, et d’autres comme l’affichage se fassent en séquentiels il suffira d’ajouter <i>parallel()</i> et <i>sequential()</i>.</p>

<h2>Conclusion</h2>
<p style="text-align: justify;">Dans cette partie on a présenté les objectifs du projet Lambda en montrant l’importance des lambda expressions dans la simplification de l’écriture de code concis et lisible. On a aussi montré comment elles réduisent le gap entre un programme séquentiel et un programme parallèle dans la manipulation des collections sans ajouter de « bruit » dans le code.</p>
<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/?p=2024">Dans le prochain billet</a> nous parlerons des principales nouveautés introduits dans le langage, notamment l’interface Stream, les interfaces fonctionnelles, les VEM et les méthodes de références. Nous verrons également comment la promesse de la « compatibilité ascendante » influe sur le choix de l’implémentation des nouveaux concepts.</p>
Pour aller plus loin

Biographie :
<ul>
	<li>Java Magazine : Septembre/Octobre 2012</li>
	<li>Programmation concurrente en java</li>
</ul>
Webographie :
<ul>
	<li>http://www.lambdafaq.org/</li>
	<li>http://cr.openjdk.java.net/~briangoetz/lambda/collections-overview.html</li>
	<li>http://java.amitph.com/2012/08/at-first-sight-with-closures-in-java.html</li>
</ul>
