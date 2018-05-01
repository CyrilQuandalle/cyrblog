---
layout: post
title: La bataille des langages à Devoxx
date: 2012-04-26 13:44
author: nouhoum traore
comments: true
categories: [Ceylon, devoxxFR, Evénements, Kotlin, Programmation]
---
<a href="http://www.arolla.fr/blog/wp-content/uploads/2012/04/Devoxx2012.png"><img class="size-medium wp-image-535 alignnone" title="Devoxx2012" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/Devoxx2012-300x162.png" alt="" width="300" height="162" /></a>

<p style="text-align: justify;">La semaine dernière, à Devoxx France 2012, c’était la battle des langages de programmation sur la JVM. Des petits nouveaux essaient de s’imposer face aux anciens, Scala, Groovy et Clojure,  qui sont relativement bien installés dans le paysage. Dans cet article, je fais un retour sur les deux nouveaux.</p>

<p style="text-align: justify;">Deux constats s’imposent au vu de la prolifération des langages sur la JVM. D’abord le langage,  Java n’arrive plus à satisfaire l’ensemble de la communauté qui semble être à la recherche d’un meilleur Java. Les langages alternatifs déjà installés ne font pas l’unanimité non plus: <a href="http://www.scala-lang.org/">Scala</a> pour cause de sa complexité (réelle ou supposée), Groovy à cause du typage dynamique (cela est <a href="http://glaforge.appspot.com/article/groovy-at-devoxx-france">en train de changer</a>). Ensuite, la JVM est une plateforme mature à laquelle sont attachés les développeurs Java et les prétendants à la succession de Java (le langage) l'ont compris et se veulent tous interopérables avec ce dernier.</p>

<p align="JUSTIFY"><span style="font-family: 'Bitstream Charter', serif; font-size: x-small;">
</span></p>

<h2>L’origine des deux langages</h2>

<p style="text-align: justify;">Kotlin et Ceylon viennent respectivement de JetBrains et de Red Hat, de deux gros utilisateurs de Java ayant une longue pratique du langage. Cette expérience pratique devrait influencer le design de Kotlin et de Ceylon.</p>

<h2>Les objectifs affichés<strong> </strong></h2>

<p style="text-align: justify;"><strong>Ceylon</strong></p>

<ul style="text-align: justify;">
    <li>Syntaxe assez <strong>proche</strong> de celle de <strong>Java</strong> : l'idée est de faciliter l'apprentissage du langage pour tous ceux qui connaissent déjà un langage dérivé du C</li>
    <li>Faciliter l'écriture de frameworks grâce notamment à la méta-programmation</li>
    <li>Immutable par défaut</li>
</ul>

<p style="text-align: justify;"><strong>Kotlin</strong></p>

<ul style="text-align: justify;">
    <li>Compilation aussi rapide qu'en Java</li>
    <li>Plus simple que Scala qui est considéré comme étant le compétiteur le plus mature</li>
</ul>

<p style="text-align: justify;"><strong>Les buts en commun</strong></p>

<p style="text-align: justify;">Faire un langage compatible avec Java mais plus sûr et plus concis</p>

<ul style="text-align: justify;">
    <li>Système de type meilleur que celui de Java</li>
    <li>Facile à apprendre</li>
    <li>Modulaire par construction</li>
</ul>

<p style="text-align: justify;">Les deux langages gagnent en concision notamment grâce l'inférence de type.</p>

<h2 align="JUSTIFY">Morceaux choisis</h2>

Maintenant voyons quelques fonctionnalités plus en détail.

Commençons par le classique « Hello world » !

Voici un hello en Ceylon :

[java]
class Hello() {
  print(&amp;quot;Hello Ceylon!&amp;quot;);
}
[/java]

<p align="JUSTIFY">En Kotlin cela donne :</p>

[scala]
fun main(args : Array) {
    println(&amp;quot;Hello Kotlin&amp;quot;)
}
[/scala]

<p style="text-align: justify;" align="JUSTIFY">Rien de révolutionnaire jusque-là ! Une remarque toutefois: en Ceylon le point-virgule est obligatoire à la fin d'une instruction contrairement à Kotlin.</p>

<p style="text-align: justify;"><strong>Null est le mal (null-safety)</strong></p>

<p style="text-align: justify;">Ceylon et Kotlin obligent tous les deux le développeur à préciser si le retour d'une fonction (ou méthode) ou une référence quelconque peut être <strong>null</strong>.</p>

<p style="text-align: justify;">Par exemple, supposons que nous souhaitons définir une fonction getNumberOfPosts() qui retourne le nombre de posts d'un blog. La définition suivante passerait en Java mais en Kotlin ou en Ceylon.</p>

En Java :

[java]
Integer getNumberOfPosts() {
  return null;
}
[/java]

En Kotlin :

[scala]
fun getNumberOfPosts(): Int? {
  return null ;
}
[/scala]

En Ceylon :

[java]
Integer? GetNumberOfPosts() {
  return null;
}
[/java]

<p style="text-align: justify;">Les deux langages ont fait le choix de faire suivre le type d'une référence d'un point d'interrogation pour indiquer qu'elle peut être null.</p>

<p style="text-align: justify;">Supposons que vous souhaitez, pour une raison qui m'est inconnue, écrire le double du nombre de posts dans une variable. Vous feriez cela comme suit en Kotlin :</p>

[scala]
val numberOfPosts = getNumberOfPosts()
val doubledNumberOfPosts = numberOfPosts?.times(2)
[/scala]

En Ceylon les choses ne sont guère différentes :

[java]
Integer? numberOfPosts = getNumberOfPosts();
Integer? doubledNumberOfPosts = numberOfPosts?.times(2);
[/java]

<p style="text-align: justify;">Cela nous amène à une autre feature intéressante des deux langages, le cast intelligent (<em>smart cast</em>).</p>

<strong>Le smart cast</strong>

En Ceylon :

[java]
if(exists numberOfPosts) {
  Integer doubledNumberOfPosts = numberOfPosts.times(2);
}
[/java]

En Kotlin :

[scala]
if (numberOfPosts is Int) {
  val doubledNumberOfPosts = numberOfPosts.times(2)
}
[/scala]

<p style="text-align: justify;">Comme vous l'avez remarqué, une fois que le test de non-nullité réussit, nous pouvons invoquer les méthodes en toute sûreté.</p>

<p style="text-align: justify;"><strong>Et l'héritage dans tout ça</strong></p>

<p style="text-align: justify;">Nous allons définir une simple hiérarchie de classes pour voir comment on définit et utilise une classe en Kotlin et Ceylon. Notre système est constitué de 3 classes : une classe de base Message avec deux implémentations, SmsMessage et EmailMessage.</p>

En Ceylon :

[java]
abstract class Message() {
  shared formal String sender();
  shared formal String receiver();
  shared formal String message();

  shared default String asString() {
    return &amp;quot;Generic message : # &amp;quot; + message();
  }
}

class SmsMessage(String senderNumber, String receiverNumber, String content) extends   Message() {
  shared actual String sender() {
    return senderNumber;
  }

  shared actual String receiver() {
    return receiverNumber;
  }

  shared actual String message() {
    return content;
  }

  shared actual String asString() {
    return &amp;quot;Sms message : # &amp;quot; + message();
  }
}

class EmailMessage(String senderEmail, String receiverEmail, String content) extends Message() {
  shared actual String sender() {
    return senderEmail;
  }

  shared actual String receiver() {
    return receiverEmail;
  }

  shared actual String message() {
    return content;
  }

  shared actual String asString() {
    return &amp;quot;Sms message : # &amp;quot; + message();
  }
}
[/java]

<p style="text-align: justify;">Plusieurs nouveaux mots-clés font leur apparition ici, <em>formal</em>, <em>default</em>, <em>actual</em> et <em>shared</em>.</p>

<ul style="text-align: justify;">
    <li><strong>shared</strong> :Les règles en Ceylon sont ridiculement simples : tout est privé par défaut et shared est utilisé pour rendre publique une méthode.</li>
    <li><strong>formal</strong> spécifie que le membre annoté ne fournit pas d'implémentation.</li>
    <li><strong>default</strong> précise une implémentation par défaut.</li>
    <li><strong>actual</strong> permet de préciser qu'on est en train de redéfinir une méthode.</li>
</ul>

<p style="text-align: justify;">Si vous codez en Java,vous ne devrez pas vous sentir dépaysé ici. Même s'ils pourraient empêcher de faire des erreurs d'inattention, je trouve que tous ces mots-clés rendent la syntaxe un peu verbeuse. Maintenant, attaquons-nous à la classe qui envoie les messages :</p>

[java]
class MessageSender() {
  shared void send(Message message) {
    switch(message)
    case (is SmsMessage) {
      print(&amp;quot;Sending SMS message...&amp;quot; + message.asString());
    }
    case (is EmailMessage) {
      print(&amp;quot;Sending email message...&amp;quot; + message.asString());
    } else {
      print(&amp;quot;Unknown message type&amp;quot;);
    }
  }
}
[/java]

<p style="text-align: justify;">Le switch n'étant pas encore implémenté, ce code ne s'exécutera pas. Je vous l'ai montré afin que vous voyiez l'élégance du 'switch-case' qui devrait à terme être implémenté dans Ceylon. Je vous donne une version simple qui a le mérite de fonctionner (véridique) :</p>

[java]
class MessageSender() {
  shared void send(Message message) {
    print(&amp;quot;Sending SMS message...&amp;quot; + message.asString());
  }
}
[/java]

<p style="text-align: justify;">Envoyons deux p'tits messages à nos amis avant d'entamer l'implémentation de notre système avec Kotlin. Notez l'absence du mot-clé <strong>new</strong>!</p>

[java]
MessageSender messageSender = MessageSender();
messageSender.send(SmsMessage(&amp;quot;01234&amp;quot;, &amp;quot;04321&amp;quot;, &amp;quot;Lol! Je te vois sur Facebook!&amp;quot;));
messageSender.send(EmailMessage(&amp;quot;toto@wesh.fr&amp;quot;, &amp;quot;titi@mdr.fr&amp;quot;, &amp;quot;Mdr! T'as vu ça!?&amp;quot;));
[/java]

En Kotlin :

[scala]
abstract class Message {
    abstract fun sender(): String
    abstract fun receiver(): String
    abstract fun message(): String
    open fun asString() = &amp;quot;Generic message # &amp;quot; + message()
}

class SmsMessage( val sender: String, val receiver: String, val message: String) : Message() {
    override fun sender() = sender
    override fun receiver(): String = receiver
    override fun message(): String = message
    override fun asString(): String = &amp;quot;SMS : &amp;quot; + message()
}

class EmailMessage(val sender: String, val receiver: String, val message: String) : Message() {
    override fun sender() = sender
    override fun receiver(): String = receiver
    override fun message(): String = message
    override fun asString(): String = &amp;quot;Email : &amp;quot; + message()
}
[/scala]

<p style="text-align: justify;">Là, un nouveau mot-clé a fait apparition: <strong>open</strong>. Les classes et les méthodes sont par défault <em>final</em> en Kotlin. C'est la raison pour laquelle la méthode est asString() est annotée avec open pour qu'on puisse la redéfinir.</p>

Le sender :

[scala]
class MessageSender {
    fun send(message: Message) {
        when (message) {
            is SmsMessage -&amp;gt; println(&amp;quot;Sending SMS message : # &amp;quot; + message.asString())
            is EmailMessage -&amp;gt; println(&amp;quot;Sending email message : # &amp;quot; + message.asString())
            else -&amp;gt; println(&amp;quot;Unknown message type.&amp;quot;)
        }
    }
}
[/scala]

Renvoyons nos messages avec le nouveau code :

[scala]
val messageSender = MessageSender()
messageSender.send(SmsMessage(&amp;quot;01234&amp;quot;, &amp;quot;04321&amp;quot;, &amp;quot;Lol! Je te vois sur Facebook!&amp;quot;))
[/scala]

<p style="text-align: justify;">Une impression de déjà vu ? Vous avez raison: la seule chose qui a changé est la déclaration de la variable messageSender.</p>

<p style="text-align: justify;"><strong>Les outils</strong></p>

<p style="text-align: justify;">Sans surprise le support de Kotlin est excellent. Quant à Ceylon, le plugin est fonctionnel.</p>

<p style="text-align: justify;">Nous allons nous arrêter là, car cet article commence à être trop long même s'il reste de nombreuses fonctionnalités intéressantes (les traits, les type union, les closures etc.) non mentionnées.</p>

<h2>Conclusion</h2>

<p style="text-align: justify;">Comme vous avez pu le constater, ces deux langages sont assez similaires à la fois dans les objectifs et dans la syntaxe. Pour voir de réelles différences, il faudrait les étudier davantage. L'avantage qu'ils ont face aux anciens y compris Java, est qu'ils peuvent apprendre des erreurs du passé. La bataille des langages sur la JVM n'est pas prêt de s'arrêter et Java, le vétéran, n'a pas dit son dernier mot car ses futures versions pourraient changer la donne. Le chemin est encore long pour rivaliser réellement avec Groovy et Scala, qui sont déjàdéployés en production chez des acteurs assez sérieux.</p>

<p style="text-align: justify;">J'espère que ce bref aperçu de quelques fonctionnalités vous a donné envie d'aller plus loin avec Kotlin et Ceylon. Un examen plus poussé est nécessaire pour mieux cerner les choix effectués par les auteurs. Je note toutefois une plus grande verbosité dans la syntaxe de Ceylon par rapport à Kotlin notamment à cause des mots-clés shared, actual etc.</p>
