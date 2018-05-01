---
layout: post
title: "Les type classes Scala : exemple sur une sérialisation MongoDB (2/2)"
date: 2012-08-28 10:00
author: nouhoum traore
comments: true
categories: [Fonctionnel, implicit parameter, mongodb, Programmation, scala, type class]
---
<p style="text-align: justify;">Dans la <a title="Les type classes Scala : exemple sur une sérialisation MongoDB (1/2)" href="http://www.arolla.fr/blog/2012/07/les-type-classes-scala-exemple-sur-une-serialisation-mongodb-12/">première partie</a>, nous avons introduit les types classes et avons créé une API pour travailler avec MongoDB en Scala. Dans cette partie, nous allons voir comment améliorer cette API grâce aux paramètres implicites.</p>
<p style="text-align: justify;">Scala donne la possibilité d’annoter les paramètres d’une méthode comme étant des paramètres “<strong><em>implicites</em></strong>”. Un paramètre implicite d’une méthode est un paramètre que nous ne sommes pas obligés de passer à la méthode lors de son invocation. Dans le cas où un paramètre implicite n’est pas passé en argument de la méthode, le compilateur Scala se débrouille pour en déterminer un. Appliqué à la méthode save de <em>MongoOperations,</em> nous obtenons la déclaration suivante :</p>

[scala]
trait MongoOperations {
  def save[T](t: T)(implicit tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Unit
    = tc.write(t) map { dbo =&gt; coll(tc.collectionName).insert(dbo) }
  ...
}
[/scala]
<p style="text-align: justify;">L’annotation <em>implicit</em> s’applique à la liste de paramètres qui la suivent, c’est-à-dire à la fois à l’instance de la type classe tc et à la fonction coll. A l’usage, cela donne :</p>

[scala]
...
implicit def collFun = (name: String) =&gt; MongoConnection()(&quot;test&quot;)(name)
//Sauvegarde
save(User(new ObjectId, &quot;Toto&quot;))
save(Task(new ObjectId, &quot;A first task&quot;, false))
val user = findOne[User](DBObject(&quot;_id&quot; -&gt; new ObjectId(&quot;50033d4344aef87888c08537&quot;)))
//Récupération
val task = findOne[Task](DBObject(&quot;_id&quot; -&gt; new ObjectId(&quot;50033d4344aef87888c08537&quot;)))
[/scala]
<p style="text-align: justify;">Maintenant, essayons de répondre à la question vous devez être en train de vous poser : comment le compilateur fait pour trouver les paramètres manquants ?</p>
<p style="text-align: justify;">Prenons par exemple cette ligne :</p>

[scala]
save(Task(new ObjectId, &quot;A first task&quot;, false))
[/scala]
<p style="text-align: justify;">Lorsque le compilateur se trouve face à cet appel, il cherche à  résoudre les paramètres implicites en regardant d’abord dans le scope courant. Il y trouve la fonction <em>collFun</em> marquée comme implicite et l’utilise à la place du dernier paramètre de la méthode <em>save</em>, la fonction qui renvoie une collection MongoDB à partir de son nom. Par contre, rien n’est défini dans le scope local pour le deuxième paramètre, l’instance de la type classe et le compilateur cherche ailleurs et finit par trouver <em>TaskMongoModel</em>, l’instance de la type classe, définie comme objet implicite dans l’objet compagnon de la classe <em>Task</em> :</p>

[scala]
object Task {
  implicit object TaskMongoModel extends MongoDbModel[Task] {
    def collectionName = &quot;tasks&quot;
    def write(task: Task): Option[DBObject] = try {
      Some(DBObject(&quot;_id&quot; -&gt; task.id, &quot;title&quot; -&gt; task.title, &quot;isDone&quot; -&gt;
      task.isDone))
    } catch {
      case _ =&gt; None
    }

    def read(dbo: DBObject): Option[Task] = try {
      Some(Task(dbo.as[ObjectId](&quot;_id&quot;), dbo.as[String](&quot;title&quot;),
        dbo.as[Boolean](&quot;isDone&quot;)))
    } catch {
      case _ =&gt; None
    }
  }
}
[/scala]
<p style="text-align: justify;">Si à la fin, le compilateur n’arrive pas à déterminer une valeur pour un paramètre implicite, une erreur est produite.</p>
<p style="text-align: justify;">Avec cette nouvelle version de notre API, nous n’avons plus besoin de fournir explicitement les paramètres qui alourdissent notre code. Nous avons tiré profit des paramètres implicites à cet effet.</p>

<h2 style="text-align: justify;">Pour finir...</h2>
<p style="text-align: justify;">Avant de conclure, faisons un petit détour par la classe <a href="http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html" target="_blank">Collections</a> de Java et notamment les méthodes <a href="http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#sort(java.util.List,%20java.util.Comparator)" target="_blank">sort</a> dont elle dispose :</p>

[java]
static &lt;T extends Comparable&lt;? super T&gt;&gt; void sort(List&lt;T&gt; list)
static &lt;T&gt; void sort(List&lt;T&gt; list, Comparator&lt;? super T&gt; c)
[/java]
<p style="text-align: justify;">La première méthode trie les éléments de la liste mais impose que leur type implémente l’interface <a href="http://docs.oracle.com/javase/6/docs/api/java/lang/Comparable.html" target="_blank">Comparable</a> de Java. Cela pose plusieurs problèmes.</p>
<p style="text-align: justify;">Le premier est que si vous devez trier une liste d’objets dont le type n’implémente pas <em>Comparable</em> et que vous n’avez pas la main sur le type en question, vous ne pourrez pas trier votre liste. Le deuxième est qu’il n’est pas possible d’implémenter deux logiques de tri pour le même type avec cette méthode.</p>
<p style="text-align: justify;">La deuxième méthode de tri définie par la classe <em>Collections</em> résout les problèmes mentionnés ci-dessus en sortant la logique de tri de la hiérarchie du type. Elle prend en paramètre une liste d’éléments d’un type quelconque <em>T</em> et un objet de type <a href="http://docs.oracle.com/javase/6/docs/api/java/util/Comparator.html" target="_blank">Comparator</a> qui permet de définir une relation d’ordre entre les dits éléments. C’est l’approche des types classes ! Vous l’aviez peut-être trouvé !</p>
<p style="text-align: justify;">Ce que nous venons de voir n’est qu’une introduction aux types classes. Elles sont utilisées à titre d’exemples dans <a href="https://github.com/mongodb/casbah" target="_blank">casbah</a>, <a href="https://github.com/scalaz/scalaz" target="_blank">scalaz</a>, <a href="https://github.com/scalaz/scalaz" target="_blank">sjson</a>. Vous serez étonné de leur puissance si vous regardez ce qu’elles permettent de faire avec <em>Scalaz</em>.</p>

<h2 style="text-align: justify;">Références</h2>
<ul>
	<li>Présentation sur la résolution des paramètres implicites : <a href="http://vimeo.com/20308847" target="_blank">http://vimeo.com/20308847</a></li>
	<li><a href="http://plastic-idolatry.com/typcls/#title" target="_blank">Typeclasses in Scala - Erik Osheim</a></li>
	<li><a href="https://speakerdeck.com/u/piotrga/p/typeclasses-monads-etc-functional-programming-in-scala-can-be-simple" target="_blank">Piotr G. Typeclasses, Monads...</a></li>
	<li><a href="http://debasishg.blogspot.fr/2010/06/scala-implicits-type-classes-here-i.html" target="_blank">Scala Implicits : Type Classes Here I Come</a></li>
</ul>
