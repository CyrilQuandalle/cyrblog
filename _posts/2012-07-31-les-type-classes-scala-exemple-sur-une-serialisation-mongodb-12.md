---
layout: post
title: "Les type classes Scala : exemple sur une sérialisation MongoDB (1/2)"
date: 2012-07-31 10:38
author: nouhoum traore
comments: true
categories: [Fonctionnel, mongodb, Programmation, scala, type class]
---
<p style="text-align: justify;">S’il y a un pattern que vous ne pouvez pas rater dans les librairies écrites en Scala ou dans les articles de blog consacrés à ce langage <span style="text-align: justify;">ce sont bien les</span> “<strong><a href="http://en.wikipedia.org/wiki/Type_class" target="_blank">type classes</a></strong>”. Une type classe offre un moyen de définir un comportement commun à plusieurs types. Elle définit une interface commune, mais pas au sens de l’héritage rencontré dans la programmation orientée objet, aux types lui appartenant. Au même titre que l’héritage, les types classes permettent de définir des opérations “polymorphiques” c’est-à-dire des opérations qui marchent avec plusieurs types. Une type classe impose une contrainte qui doit être satisfaite par les types souhaitant utiliser son comportement. Afin de faciliter la compréhension de nos propos, voyons un exemple : la type classe <a title="la type classe Ordering" href="http://www.scala-lang.org/api/current/scala/math/Ordering.html" target="_blank">Ordering</a> définie par la librairie standard de Scala en triant le contenu d'une liste.</p>

<h2 style="text-align: justify;">Trier les éléments d’une liste</h2>

<p style="text-align: justify;">Nous allons demander à Scala de nous trier une collection de nombres entiers. Lançons le REPL pour passer à l’action :</p>

[scala]
scala&gt; val nums = List(2, 4, 9, 33, 9, 3, 11)
nums: List[Int] = List(2, 4, 9, 33, 9, 3, 11)

scala&gt; val sortedNums = nums.sorted
sortedNums: List[Int] = List(2, 3, 4, 9, 9, 11, 33)
[/scala]

<p style="text-align: justify;">Nous avons créé une liste de nombres entiers puis avons demandé à Scala de la trier en invoquant la méthode sorted. Cette méthode nous retourne une liste triée. Plutôt facile ! Maintenant que se passe-t-il si nous souhaitons trier, non pas une liste d’entiers mais une liste d’objets quelconques ? Pour cela, imaginons que nous disposons d’une liste de joueurs constitués d’un nom et d’un score.</p>

[scala]
case class Player(name: String, score: Int)
[/scala]

<p style="text-align: justify;">Lançons le REPL et faisons comme plus haut :</p>

[scala]
scala&gt; case class Player(name: String, score: Int)
defined class Player

scala&gt; val players = List(Player(&quot;John Doe&quot;, 19), Player(&quot;Jane Doe&quot;, 34), Player(&quot;Toto Titi&quot;, 10))
players: List[Player] = List(Player(John Doe,19), Player(Jane Doe,34), Player(Toto Titi,10))
scala&gt; players.sorted
&lt;console&gt;:11: error: No implicit Ordering defined for Player.
              players.sorted
                      ^
[/scala]

<p style="text-align: justify;">Cette fois-ci les choses ne se sont pas si bien passées ! Le message d’erreur explique qu’aucun “Ordering” n’est défini de manière implicite pour le type Player. Voici l’explication : la méthode sorted s’attend à trouver une instance de la type classe Ordering pour le type Player et nous obtenons une erreur puisqu’une telle instance n’existe pas. Définissons-en une en se basant sur le score des joueurs et réessayons de trier notre liste :</p>

[scala]
scala&gt; implicit object personScoreOrdering extends Ordering[Player] {
     |   override def compare(p1: Player, p2: Player) = p1.score compare p2.score
     | }
defined module personScoreOrdering

scala&gt; players.sorted
res0: List[Player] = List(Player(Toto Titi,10), Player(John Doe,19), Player(Jane Doe,34))
[/scala]

<p style="text-align: justify;">Cette fois-ci, la liste des joueurs est bien triée comme en atteste la sortie du REPL. Il a suffit de définir une instance de la type classe pour notre classe pour pouvoir bénéficier de l’opération de tri. En plus, nous n’avons apporté aucune modification à la classe Player pour y arriver. Après ces premiers pas, nous allons travailler sur un problème pour mieux comprendre des types classes : sérialiser des objets de notre application afin de permettre à <a href="http://www.mongodb.org/" target="_blank">MongoDB</a> de les persister.</p>

<h2 style="text-align: justify;">Les type classes et Mongo DB</h2>

<p style="text-align: justify;">Quand vous travaillez directement avec le <a href="https://github.com/mongodb/casbah" target="_blank">driver Scala</a> de Mongo DB sans passer par un framework de mapping objet/document comme <a href="http://code.google.com/p/morphia/" target="_blank">Morphia</a> ou <a href="https://github.com/novus/salat/" target="_blank">Salat</a>, vous avez besoin de transformer vos objets métier en DBObject afin de les persister. De même lorsque vous lisez un document MongoDB, vous obtenez un objet DBObject que vous transformez en un objet du domaine avant d’en faire quelque chose. Le listing suivant définit une interface à cet effet.</p>

[scala]
package fr.arolla.blog.mongo
import com.mongodb.casbah.Imports._

trait MongoDbModel[T] {
  def collectionName: String
  def write(t: T): Option[DBObject]
  def read(dbo: DBObject): Option[T]
}
[/scala]

<p style="text-align: justify;">Elle inclut aussi une fonction qui permet d’obtenir le nom de la collection dans laquelle sont stockés les documents correspondants à notre modèle. <em>MongoDbModel</em> est la type classe. Voyons comment elle est utilisée en définissant le code qui interagit avec Mongo pour stocker nos objets, les lister ou les supprimer :</p>

[scala]
package fr.arolla.blog.mongo
import com.mongodb.casbah.Imports._

trait MongoOperations {
  def save[T](t: T)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Unit
  def findOne[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Option[T]
  def findById[T](id: ObjectId)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Option[T]
  def find[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Seq[T]
  def delete[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Unit
  def count[T](tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Long
}
[/scala]

<p style="text-align: justify;">Le trait <em>MongoOperations</em> définit les opérations CRUD avec la base. Attardons-nous un peu sur la signature des méthodes. Le paramètre tc de type <em>MongoDbModel</em> sert à convertir un objet métier t en une instance de <em>DBObject</em> qui est persisté par MongoDB ou de faire la transformation inverse. Le paramètre coll est une fonction qui retourne un objet de type <em>MongoCollection</em> en prenant son nom en paramètre. Par exemple, la méthode <em>save</em> utilise le paramètre tc pour convertir l’objet métier t en une instance de <em>DBObject</em> qui est persisté par MongoDB dans la collection renvoyée par la fonction coll. Implémentons les méthodes puisqu’un trait peut contenir des définitions :</p>

[scala]
package fr.arolla.blog.mongo
import com.mongodb.casbah.Imports._

trait MongoOperations {
  def save[T](t: T)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Unit =
    tc.write(t) map { dbo =&gt; coll(tc.collectionName).insert(dbo) }

  def findOne[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection):
    Option[T] = coll(tc.collectionName).findOne(query).flatMap { tc.read _ }

  def findById[T](id: ObjectId)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection):
    Option[T] = coll(tc.collectionName).findOneByID(id).flatMap { tc.read _ }

  def find[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection):
    Seq[T] = coll(tc.collectionName).find(query).map { tc.read _ }.flatten.toSeq

 def delete[T](query: DBObject)(tc: MongoDbModel[T], coll: String =&gt; MongoCollection):
    Unit = coll(tc.collectionName).remove(query)

 def count[T](tc: MongoDbModel[T], coll: String =&gt; MongoCollection): Long =
   coll(tc.collectionName).count
}
[/scala]

<p style="text-align: justify;">Définissons deux classes simples pour pouvoir tester notre code :</p>

[scala]
package fr.arolla.blog.domain
import org.bson.types.ObjectId

case class User(id: ObjectId, name: String)
case class Task(id: ObjectId, title: String, isDone: Boolean)
[/scala]

<p style="text-align: justify;">Pour pouvoir utiliser <em>MongoOperations</em> avec nos classes, il nous faut pour chacune une instance de la type classe. Nous en définissons une dans les objets compagnons ("companion objects") comme suit :</p>

<p style="text-align: justify;"><em>Companion object de User :</em></p>

[scala]
object User {
  implicit object UserMongoModel extends MongoDbModel[User] {
    def collectionName: String = &quot;users&quot;
    def write(user: User): Option[DBObject] = try {
      Some(DBObject(&quot;_id&quot; -&gt; user.id, &quot;name&quot; -&gt; user.name))
    } catch {
      case _ =&gt; None
    }

    def read(dbo: DBObject): Option[User] = try {
      Some(User(dbo.as[ObjectId](&quot;_id&quot;), dbo.as[String](&quot;name&quot;)))
    } catch {
      case _ =&gt; None
    }
   }
}
[/scala]

<p style="text-align: justify;">Companion object de Task :</p>

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

<p style="text-align: justify;">Vous aurez besoin d’une installation de MongoDB pour pouvoir lancer les tests. Rendez-vous sur <a href="http://www.mongodb.org/" target="_blank">mongodb.org</a> pour plus d’information sur l’installation.</p>

<p style="text-align: justify;">Testons notre code dans le REPL :</p>

[scala]
scala&gt; import fr.arolla.blog.mongo._
import fr.arolla.blog.mongo._
scala&gt; object MongoService extends MongoOperations {}
defined module MongoService
scala&gt; import com.mongodb.casbah.Imports._
import com.mongodb.casbah.Imports._
scala&gt; def collFun = (name: String) =&gt; MongoConnection()(&quot;test&quot;)(name)
collFun: String =&gt; com.mongodb.casbah.MongoCollection
scala&gt; import fr.arolla.blog.domain._
import fr.arolla.blog.domain._
scala&gt; val user = User(new ObjectId, &quot;Toto&quot;)
user: fr.arolla.blog.domain.User = User(5010035d44ae79f41b026ad3,Toto)
scala&gt; import MongoService._
import MongoService._
scala&gt; save(user)(User.UserMongoModel, collFun)
[/scala]

<p style="text-align: justify;">Nous avons défini d'abord un service qui implémente l'interface <em>MongoOperations</em> puis avons créé et persisté un utilisateur dans la base. Avant de tester les autres méthodes, persistons un autre type d'objet : une tâche.</p>

[scala]
scala&gt; val task = Task(new ObjectId, &quot;A first task&quot;, false)
task: fr.arolla.blog.domain.Task = Task(501005b544aed38335cfe824,A first task,false)
scala&gt; save(task)(Task.TaskMongoModel, collFun)
[/scala]

<p style="text-align: justify;">Là aussi, tout à l'air de bien se passer. La méthode save a été capable de persister les deux types d'objet. Ceci dit une vérification s'impose ! Nous allons récupérer le contenu de la base avec la méthode <em>findById</em>.</p>

[scala]
scala&gt;findOne[User](DBObject(&quot;_id&quot; -&gt; new ObjectId(&quot;5010035d44ae79f41b026ad3&quot;)))(User.UserMongoModel, collFun)
res2: Option[fr.arolla.blog.domain.User] = Some(User(5010035d44ae79f41b026ad3,Toto))
[/scala]

<p style="text-align: justify;">Nous arrivons bien à récupérer l'utilisateur précédemment stocké dans la base. Et si je fais la requête avec un mauvais id ?</p>

[scala]
scala&gt;findOne[User](DBObject(&quot;_id&quot; -&gt; new ObjectId(&quot;5010035d44ae79f41b026ad4&quot;)))(User.UserMongoModel, collFun)
res3: Option[fr.arolla.blog.domain.User] = None
[/scala]

<p style="text-align: justify;">Dans ce cas, le service ne trouve rien car aucun objet ne correspond à cet identifiant que j’ai créé de toute pièce !</p>

<p style="text-align: justify;">Tout va bien jusque-là. Nous avons réussi à définir un service qui fonctionne avec tout objet qui fournit une instance de la type classe. L’instance de la type classe permet d’<strong><em>adapter</em></strong> les types à notre interface. Cela doit rappeler le design <a title="Le design pattern adaptateur" href="http://en.wikipedia.org/wiki/Adapter_pattern" target="_blank">pattern adaptateur</a> à certains. Notre implémentation marche même avec les types que nous ne pouvons pas modifier. Il subsiste toutefois un problème : la lourdeur de notre API. A chaque appel, l'utilisateur doit fournir à la fois une instance de la type classe et la fonction qui renvoie la collection MongoDB dans laquelle l'objet est stocké.</p>

<a title="Les type classes Scala : exemple sur une sérialisation MongoDB (2/2)" href="http://www.arolla.fr/blog/2012/08/les-type-classes-scala-exemple-sur-une-serialisation-mongodb-22/" target="_blank">Dans la deuxième partie de ce post, nous allons résoudre ce problème grâce à la possibilité de passer des paramètres de manière implicite.</a>
