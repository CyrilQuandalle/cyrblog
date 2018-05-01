---
layout: post
title: Il y a peut être une option pour continuer ¡¿ (réflexion sur la programmation par continuation)
date: 2012-12-05 15:35
author: arnauld loyer
comments: true
categories: [continuation, Fonctionnel, java, maybe, option, Programmation, scala]
---
<p style="text-align: justify;">L'une des difficultés principales lorsque l'on aborde la programmation par événement est qu'il faut changer sa manière de penser: l'appel d'une méthode ne renvoie pas de résultat. Lorsque le résultat est disponible, celui-ci est à son tour publié sur un bus ou fourni à une fonction de rappel passée en paramètre lors de l'appel. C'est cette dernière option que nous allons aborder dans cet article: la programmation par continuation.</p>

<p style="text-align: justify;"><!--more--></p>

<blockquote>En programmation fonctionnelle, la programmation par continuation désigne une technique de programmation consistant à n'utiliser que de simples appels de fonction qui prennent pour argument leur propre continuation, au lieu d'appeler séquentiellement des fonctions, ou d'exécuter une fonction sur le résultat de la précédente. Ces fonctions se retrouvent en quelque sorte maîtresses de leur destin, et ne se contentent plus de subir le contexte.

<a href="http://fr.wikipedia.org/wiki/Continuation">Continuation</a> ~ Wikipedia</blockquote>

<p style="text-align: justify;">Le schéma de pensée doit alors s'orienter vers une méthode de programmation plus fonctionnelle<sup><a href="#func1">1</a></sup> que procédurale.
Bien que cela paraisse plus compliqué à mettre en place au départ, cela permet une souplesse et une modularisation beaucoup plus grande et une testabilité facilitée.</p>

<p style="text-align: justify;"><em><a name="func1"></a>[1] Nous parlons ici de programmation fonctionnelle au sens de fonction uniquement et non son paradigme standard qui prône l'immutabilité des données. Une fonction devient un citoyen de premier ordre au même titre qu'une classe. Java n'ayant pas cette dimension, nous ferons une utilisation intensive des classes anonymes pour compenser cela.</em></p>

<blockquote>
<h2>Des fonctions passées en paramètre</h2>
(...) Un mécanisme puissant des langages fonctionnels est l'usage des fonctions d'ordre supérieur. Une fonction est dite d'ordre supérieur lorsqu'elle peut prendre des fonctions comme argument (aussi appelées callback) et/ou renvoyer une fonction comme résultat. On dit aussi que les fonctions sont des objets de première classe, ce qui signifie qu'elles sont manipulables aussi simplement que les types de base.

<a href="http://fr.wikipedia.org/wiki/Programmation_fonctionnelle">Programmation fonctionnelle</a> ~ Wikipedia</blockquote>

<p style="text-align: justify;">Nous allons voir plusieurs techniques permettant de faciliter l'intégration d'une approche fonctionnelle à un code orienté objet. Le but n'est pas de tout écrire dans un paradigme ou un autre, mais simplement de voir comment une approche peut compléter et enrichir l'autre.
Après avoir défini nos techniques de bases, nous les appliquerons plus concrètement à différents cas d'utilisation.</p>

<p style="text-align: justify;">Prenons comme base de travail une application qui gère des questionnaires (Quiz).</p>

<h2 style="text-align: justify;">"Sans technique, la puissance n'est rien" <em>— Aurait aussi pu dire le pneu</em></h2>

<p style="text-align: justify;">Notre application doit tout d'abord permettre de créer un questionnaire.
Afin de permettre la récupération du questionnaire créé, il est nécessaire de définir une fonction de rappel. Optons pour une approche générique et réutilisable (les noms des interfaces que nous définirons reprennent les noms standards que l'on retrouve en Haskell, <a href="http://www.scala-lang.org/">Scala</a> ou encore la librairie <a href="http://functionaljava.org">FunctionalJava</a>.</p>

[java]
public interface Effect&amp;amp;amp;amp;amp;lt; T&amp;amp;amp;amp;amp;gt; {
  void e(T value);
}
[/java]

<p style="text-align: justify;">Cette interface décrit une méthode abstraite prenant un unique paramètre. Notre service pourra alors appeler cette fonction<sup><a href="#func2">2</a></sup> avec le <tt>quiz</tt> qu'il aura tout juste créé.</p>

<p style="text-align: justify;"><em><a name="func2"></a>[2] Nous utiliserons le mot "fonction" bien qu'elle ne renvoie aucun résultat et au risque d'en choquer certain nous n'utiliserons pas le mot procédure car il nous détournerait de notre vision: la lourdeur des procédures n'a d’intérêt que pour les <strong>fonction</strong>aires.</em></p>

<p style="text-align: justify;">La première version de notre service peut s'écrire:</p>

[java]
public class QuizService {
  ...
  public void create(String quizContent, Effect&amp;amp;amp;amp;amp;lt;Quiz&amp;amp;amp;amp;amp;gt; effect) {
    Quiz quiz = quizFactory.create(nextId(), quizContent);
    effect.e(quiz);
  }
}
[/java]

<p style="text-align: justify;">Notre service délègue la création du <tt>quiz</tt> à la fabrique (ligne 4). La fonction de rappel <tt>effect</tt> passée en paramètre est ensuite invoquée avec l'instance nouvellement créée (ligne 5).</p>

<p style="text-align: justify;">Nous venons de voir notre première technique:</p>

<h4 style="text-align: justify;"><span class="label notice">Première technique</span></h4>

<p style="text-align: justify;"><strong>Ajouter une fonction supplémentaire comme paramètre lors de l'appel d'une méthode. Cette fonction pourra alors être appelée avec le résultat du calcul lorsque celui sera disponible.</strong></p>

<p style="text-align: justify;">Imaginons maintenant que la construction d'une nouvelle instance de quiz nécessite plusieurs vérifications: il est impératif qu'un <tt>quiz</tt> soit unique (sinon il devient trop facile de tricher). Rajoutons pour cela un appel afin de vérifier cet invariant:</p>

[java]
public class QuizService {
  public void create(String quizContent, Effect&amp;amp;amp;amp;amp;lt;Quiz&amp;amp;amp;amp;amp;gt; effect) {
    if(quizIsUnique(quizContent)) {
        Quiz quiz = quizFactory.create(nextId(), quizContent);
        effect.e(quiz);
      }
    }
}
[/java]

<p style="text-align: justify;">Bon rien de très fantastique, si ce n'est un nouveau souci: que se passe-t-il si notre quiz n'est pas unique !?</p>

<p style="text-align: justify;">C'est là que notre deuxième technique intervient!</p>

<p style="text-align: justify;">Auparavant définissons une nouvelle interface générique permettant de décrire une alternative entre deux types de valeurs <tt>L</tt> et <tt>R</tt>. Il est de coutume d’appeler les différentes alternatives "Left" et "Right" (toute ressemblance avec un contexte politique est purement fortuite). Une instance de cette interface peut donc soit contenir une instance de type <tt>L</tt> soit une instance de type <tt>R</tt>.</p>

[java]
public interface Either&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; {
  boolean isLeft();
  L left();
  boolean isRight();
  R right();
}
[/java]

&nbsp;

[java]
public class Eithers {
  public static &amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; Either&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; left(L value) {
    return new Left(value);
  }
  public static &amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; Either&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; right(L value) {
    return new Right(value);
  }
}
[/java]

<p style="text-align: justify;">L'implémentation "Left" correspondante peut alors s'écrire:</p>

[java]
public final class Left&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; implements Either&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; {
  private final L value;
  public Left(L value)    { this.value = value; }
  public boolean isLeft() { return true;  }
  public L left()         { return value; }
  public boolean isRight(){ return false; }
  public R right()        { throw new IllegalStateException(&amp;amp;amp;amp;amp;quot;Sorry only left is allowed!&amp;amp;amp;amp;amp;quot;); }
}
[/java]

<p style="text-align: justify;">On peux aisément en déduire l'implémentation "Right":</p>

[java]
public final class Right&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; implements Either&amp;amp;amp;amp;amp;lt;L,R&amp;amp;amp;amp;amp;gt; {
  private final R value;
  public Right(R value)   { this.value = value; }
  public boolean isLeft() { return false; }
  public L left()         { throw new IllegalStateException(&amp;amp;amp;amp;amp;quot;Sorry only right is allowed!&amp;amp;amp;amp;amp;quot;); }
  public boolean isRight(){ return true;  }
  public R right()        { return value; }
}
[/java]

<p style="text-align: justify;">Et un petit exemple avec une méthode qui divise un entier par un autre:</p>

<ul style="text-align: justify;">
    <li>soit le dénominateur est différent de zéro et tout va bien, la fonction renvoie le résultat</li>
    <li>soit le dénominateur est égale à zéro, la fonction renvoie alors un message indiquant que ça va pas</li>
</ul>

[java]
public static Either&amp;amp;amp;amp;amp;lt;Float,String&amp;amp;amp;amp;amp;gt; div(int numerator, int denominator) {
  if(denominator!=0) {
    float res = (float)numerator / (float)denominator;
    return Eithers.left(res);
  }
  else {
    return Eithers.right(&amp;amp;amp;amp;amp;quot;Divide by zero error&amp;amp;amp;amp;amp;quot;);
  }
}
[/java]

<p style="text-align: justify;">Revenons à la création de notre <tt>quiz</tt>: soit il est unique et tout va bien soit il ne l'est pas et ça va pas... ça ressemble fort à notre alternative. Modifions alors la signature de notre fonction de rappel afin de prendre comme résultat une alternative: notre fonction de rappel <tt>Effect</tt> devient alors <tt>Effect&lt;Either&gt;</tt>.</p>

[java]
public class QuizService {
  public void create(String quizContent, Effect&amp;amp;amp;amp;amp;lt;Either&amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;gt;&amp;amp;amp;amp;amp;gt; effect) {
    if(quizIsUnique(quizContent)) {
      Quiz quiz = quizFactory.create(nextId(), quizContent);
      Either&amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;gt; left = Eithers.left(quiz);
      effect.e(left);
    }
    else {
      // Failure is a Pojo but could also be an UniqueConstraintException
      Failure failure = new Failure(Code.NonUniqueQuiz);
      Either&amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;gt; right = Eithers.right(failure);
      effect.e(right);
    }
  }
}
[/java]

<p style="text-align: justify;">Soit le <tt>quiz</tt> est unique (ligne 3) dans ce cas l'alternative est construite avec le <tt>quiz</tt> - alternative <tt>left</tt> - (ligne 5) puis passée à la fonction de rappel (ligne 6). Dans le cas contraire, l'alternative est construite autour d'un code d'erreur - alternative <tt>right</tt> - (ligne 11) puis passée à la fonction de rappel (ligne 12).</p>

<p style="text-align: justify;">Et voila notre deuxième technique:</p>

<h4 style="text-align: justify;"><span class="label notice">Deuxième technique</span></h4>

<p style="text-align: justify;"><strong>La fonction de rappel est définie comme prenant un résultat dont le type peut varier en fonction du déroulement du calcul...</strong></p>

<p style="text-align: justify;">Un petit aperçu de code appelant:</p>

[java]
quizService.create(&amp;amp;amp;amp;amp;quot;&amp;amp;amp;amp;amp;lt;question4aChampion&amp;amp;amp;amp;amp;gt;...&amp;amp;amp;amp;amp;quot;, new Effect&amp;amp;amp;amp;amp;lt;Either&amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;gt;&amp;amp;amp;amp;amp;gt;() {
  public void e(Either&amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;gt; res) {
    if(res.isLeft()) {
      Quiz quiz = res.left();
      displayFlashFeedback(quiz);
    }
    else {
      Failure failure = res.right();
      displayErrorFeedback(failure);
    }
  }
});
[/java]

<p style="text-align: justify;">Lorsque la fonction de rappel est invoquée, soit l'alternative passée en paramètre contient un <tt>quiz</tt> (ligne 4) dans ce cas on affiche un beau message de retour avec le <tt>quiz</tt> créé. Sinon on affiche une notification d'erreur (ligne 9).</p>

<p style="text-align: justify;">Ah, quelqu'un au fond de la salle a une remarque: "Comme il s'agit d'une erreur, pourquoi ne pas lancer une exception au lieu de faire une alternative, soit on a le résultat soit on lance une exception?"</p>

<p style="text-align: justify;">Hummmm... eh bien sans trop anticiper sur la suite de l'article, il faut envisager que l’exécution du code de la méthode puisse être asynchrone.
Le contenu de la méthode s’exécute alors dans un autre fil d’exécution que le code qui l'a invoqué. Le code qui l'a invoqué continue à vivre son petit bonhomme de chemin et peut même ré-invoquer la même méthode, avant que la première exécution soit terminée. Si la méthode génère une exception, celle-ci sera dans le fil qui exécute le contenu de la méthode, l'appelant originel ne sera donc jamais informé, sauf si on ajoute un mécanisme du type <tt>UncaughtExceptionHandler</tt> qui se trouve n'être rien d'autre qu'une fonction de rappel appelée dans le cas d'erreur. En centralisant, les appels valides et invalides dans une unique fonction de rappel, le code est simplifié, ainsi que la vérification que notre fonction de rappel est appelée systématiquement.</p>

<p style="text-align: justify;">Ok et la troisième technique alors? Nous y sommes presque!
Notre Quiz étant désormais créé, il faut le persister, et pour cela il nous faut une méthode pour le sauvegarder:</p>

[java]
public class QuizService {
  public void save(Quiz quiz) {
    repository.save(quiz);
  }
}
[/java]

<p style="text-align: justify;">Hummmm... une méthode sans retour, difficile de définir une fonction de rappel. Compliquons un peu les choses: la sauvegarde peut échouer et lancer une exception.</p>

[java]
public class QuizService {
  public void save(Quiz quiz) {
    try {
        repository.save(quiz);
    }
    catch(RepositoryException re) {
        Failure failure = new Failure(re);
        ...
    }
  }
}
[/java]

<p style="text-align: justify;">Nous nous retrouvons dans le cas précédent d'une alternative mais qui n'a qu'une seule possibilité, autrement dit un résultat optionnel: on a une erreur ou pas! Si tout se passe bien, on a pas de résultat, sinon on a une erreur. En s'inspirant de notre interface <tt>Either</tt> nous pouvons définir une nouvelle interface générique qui contient (ou pas!) quelque chose:</p>

[java]
public interface Option&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; {
  boolean isSome();
  boolean isNone();
  E get();
}
[/java]

&nbsp;

[java]
public class Options {
  public static &amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; Option&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; some(E value) {
    return new Some(value);
  }
  public static &amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; Option&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; none() {
    return new None();
  }
}
[/java]

<p style="text-align: justify;">Cette interface n'a que deux implémentations: une qui contient rien <tt>None</tt> et une qui contient quelque-chose <tt>Some</tt>.</p>

<p style="text-align: justify;">L'implémentation "Some" peut alors s'écrire:</p>

[java]
public final class Some&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; extends Option&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; {
  private final E value;
  public Some(E value)    { this.value = value; }
  public boolean isSome() { return true;  }
  public boolean isNone() { return false; }
  public E get()          { return value; }
}
[/java]

<p style="text-align: justify;">L'implémentation "None":</p>

[java]
public final class None&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; extends Option&amp;amp;amp;amp;amp;lt;E&amp;amp;amp;amp;amp;gt; {
  public None() {}
  public boolean isSome() { return false;  }
  public boolean isNone() { return true; }
  public E get()          { throw new IllegalStateException(&amp;amp;amp;amp;amp;quot;Sorry nothing to retrieve!&amp;amp;amp;amp;amp;quot;); }
}
[/java]

<p style="text-align: justify;">Illustrons cela avec notre méthode de sauvegarde:</p>

[java]
public class QuizService {
  public void save(Quiz quiz, Effect&amp;amp;amp;amp;amp;lt;Option&amp;amp;amp;amp;amp;lt;Failure&amp;amp;amp;amp;amp;gt;&amp;amp;amp;amp;amp;gt; effect) {
    try {
      repository.save(quiz);
      effect.e(Options.none());
    }
    catch(RepositoryException re) {
      Failure failure = new Failure(re);
      effect.e(Options.some(failure));
    }
  }
}
[/java]

<p style="text-align: justify;">Si la sauvegarde se passe bien (pas d'exception) la fonction de rappel est appelée avec "rien": <tt>Options.none()</tt> (ligne 5). Dans le cas contraire, l'exception est transformée en un objet plus adapté <tt>Failure</tt> et la fonction de rappel est appelée avec "quelque-chose": <tt>Options.some(failure)</tt> (ligne 9).</p>

<p style="text-align: justify;">Un petit aperçu de code appelant:</p>

[java]
quizService.save(quiz, new Effect&amp;amp;amp;amp;amp;lt;Option&amp;amp;amp;amp;amp;lt;Failure&amp;amp;amp;amp;amp;gt;&amp;amp;amp;amp;amp;gt;() {
  public void e(Option&amp;amp;amp;amp;amp;lt;Failure&amp;amp;amp;amp;amp;gt; res) {
    if(res.isNone()) {
      displayFlashFeedback(Code.QuizSaved);
    }
    else {
      displayErrorFeedback(res.some());
    }
  }
});
[/java]

<p style="text-align: justify;">Soit la fonction de rappel est appelée avec "rien" (ligne 3) dans ce cas on affiche un beau message indiquant que la sauvegarde s'est bien passée. Dans le cas contraire (ligne 6), l'objet <tt>Failure</tt> est récupérée et un message d'erreur est affiché.</p>

<h4 style="text-align: justify;"><span class="label notice">Troisième technique</span></h4>

<p style="text-align: justify;"><strong>La fonction de rappel est définie comme prenant un résultat dont le contenu est optionnel</strong></p>

<p style="text-align: justify;">Avant d'exploiter tout cela, faisons un petit retour en arrière... Et si nous étions sûr que la sauvegarde se déroule toujours correctement, et que nous souhaitions juste être notifiés lorsque celle-ci a été réalisée, nous n'avons pas de technique pour cela! Effectivement, il n'y en a pas, mais l'on pourrait en ajouter une très simple: la fonction de rappel est définie comme ne prenant aucun argument et est invoquée lorsque l'action est arrivée au stade adéquat.
L'interface <tt>Runnable</tt> peut alors tout à fait correspondre à ce cas d'utilisation.</p>

[java]
public class QuizService {
  public void save(Quiz quiz, Runnable onceSaved) {
    repository.save(quiz);
    onceSaved.run();
  }
}
[/java]

<h4 style="text-align: justify;"><span class="label notice">Quatrième technique</span></h4>

<p style="text-align: justify;"><strong>La fonction de rappel est définie comme une fonction ne prenant aucun paramètre, elle est invoquée pour signaler que l'action désirée est effectuée</strong></p>

<p style="text-align: justify;">Pourquoi ne pas avoir parlé de cette technique auparavant: et bien tout simplement parce qu'il n'est généralement pas souhaitable de l'utiliser. En effet cette technique crée une ambiguïté: <strong>pourquoi notre fonction de rappel n'est pas appelée?</strong> la méthode a-t-elle oublié d’appeler la fonction de rappel, une erreur a modifié le fil d’exécution et le code n'appelle plus la fonction de rappel. <strong>En l'absence de retour, il n'est pas possible de réagir</strong>.</p>

<h2 style="text-align: justify;">"The amateur software engineer is always in search of magic." <em>— Grady Booch</em></h2>

<p style="text-align: justify;">Continuons par une petite digression qui illustrera très simplement l’intérêt d'une approche par continuation plutôt que l'approche plus traditionnelle d'une méthode avec retour.</p>

<p style="text-align: justify;">La création d'un <tt>quiz</tt> est une chose relativement complexe (si! si!) et peut prendre beaucoup de temps. Dans le cas d'une approche traditionnelle, l'appelant de notre méthode est donc en attente d'un retour, et son traitement est suspendu. Grâce à notre approche par continuation, il devient très facile de modifier le comportement de notre service afin de rendre ses traitements asynchrones, l'appelant peut alors continuer à effectuer ses propres tâches pendant ce temps.</p>

<p style="text-align: justify;">Voyons comment rendre très simplement un service asynchrone avec <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/lang/reflect/Proxy.html">Java Proxy</a>. La méthode que nous allons voir repose sur les API du jdk, la seule limitation est que cela nous oblige à définir une interface pour notre service.</p>

[java]
public interface QuizService {
  void create(final String quizContent, final Effect effect);
  void save(Quiz quiz, Effect&amp;amp;amp;amp;amp;amp;lt;Option&amp;amp;amp;amp;amp;amp;gt; effect);
}
[/java]

<p style="text-align: justify;">Notre service peux devenir asynchrone grâce à l'appel suivant:</p>

[java]
QuizService serviceImpl = new QuizServiceImpl();
QuizService asynService = Async.asyncProxy(QuizService.class,
                                           serviceImpl, executor);
[/java]

<p style="text-align: justify;">Avec comme code pour notre classe utilitaire <tt>Async</tt>:</p>

[java]
import java.lang.reflect.*;
import java.util.Arrays;
import java.util.concurrent.*;

public class Async {

  public static &amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; T asyncProxy(Class type, T impl,
                                 ExecutorService executor) {
    return new Async(executor).asyncProxy(type, impl);
  }

  private final ExecutorService executor;
  public Async(ExecutorService executor) {
    this.executor = executor;
  }

  @SuppressWarnings(&amp;amp;amp;amp;amp;quot;unchecked&amp;amp;amp;amp;amp;quot;)
  public &amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; T asyncProxy(Class&amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; type, T impl) {
    return (T)Proxy.newProxyInstance(getClassLoader(),
                                     asArray(type),
                                     asyncHandler(impl));
  }

  private static &amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; Class&amp;amp;amp;amp;amp;lt;?&amp;amp;amp;amp;amp;gt;[] asArray(Class&amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; type) {
    return new Class[]{type};
  }

  protected ClassLoader getClassLoader () {
    return getClass().getClassLoader();
  }

  protected &amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; InvocationHandler&amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; asyncHandler(T impl) {
    return new AsyncHandler&amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt;(impl, executor);
  }

  private static class AsyncHandler&amp;amp;amp;amp;amp;lt;T&amp;amp;amp;amp;amp;gt; implements InvocationHandler {
    private final T impl;
    private final ExecutorService executor;

    public AsyncHandler(T impl, ExecutorService executor) {
      this.impl = impl;
      this.executor = executor;
    }

    @Override
    public Object invoke(Object proxy,
                         final Method method,
                         final Object[] args) throws Throwable {
      Future&amp;amp;amp;amp;amp;lt;Object&amp;amp;amp;amp;amp;gt; future = executor.submit(new Callable&amp;amp;amp;amp;amp;lt;Object&amp;amp;amp;amp;amp;gt;() {
        @Override
        public Object call() throws Exception {
          return method.invoke(impl, args);
        }
      });
      Class&amp;amp;amp;amp;amp;lt;?&amp;amp;amp;amp;amp;gt; returnType = method.getReturnType();
      if(returnType==null || returnType==Void.class)
        return null;
      else
        return future.get();
    }
  }
}
[/java]

<p style="text-align: justify;">Quelques explications: un proxy est créé et implémente l'interface de notre service passée en paramètre <tt>type</tt> (ligne 18). Tous les appels effectués sur le proxy sont redirigés sur le <tt>InvocationHandler</tt> qui lui a été associé (ligne 21), et c'est là que les choses deviennent intéressantes: lignes 49 à 60.</p>

<p style="text-align: justify;">L'appel de la méthode est transformé en un fragment executable (<tt>new Callable() {...}</tt>) qui est soumis à l'<tt>Executor</tt> correspondant. La méthode est alors invoquée de manière asynchrone (par rapport à l'appelant) sur l'instance de service qui a été transformé: <tt>impl</tt> (passée en paramètre ligne 18): l'appel effectif est déclaré ligne 52. Et là, soyons malin:</p>

<ul style="text-align: justify;">
    <li>soit la méthode invoquée ne renvoie rien <tt>void</tt>, dans ce cas le code appelant n'attend aucune valeur en retour, et il n'est pas nécessaire de rendre cet appel bloquant. On sort donc de la méthode (ligne 57) même si notre <tt>Callable</tt> n'a pas encore été executé ou s'il est en cours d'execution.</li>
    <li>soit la méthode invoquée renvoie quelque chose, dans ce cas le code appelant s'attend à un retour... il faut lui renvoyer quelque chose: le code appelant va donc être suspendu (<tt>future.get()</tt>) jusqu'à ce que le resultat soit disponible (ligne 59).</li>
</ul>

<p style="text-align: justify;">Bien entendu, nous nous arrangerons pour être toujours dans le premier cas si nous voulons que les appels soient toujours asynchrones.</p>

<p style="text-align: justify;">Exemple de code appelant:</p>

[java]
quizService.create(&amp;amp;amp;amp;amp;quot;...&amp;amp;amp;amp;amp;quot;, new Effect() {
  public void e(Quiz quiz) {
    displayFlashFeedback(quiz);
  }
});
// Quiz is being created...
// ... in the meanwhile let's display some waiting feedback
displayWaitingFeedback();
[/java]

<p style="text-align: justify;"><strong>Nous voyons que sans modifier le code appelant, notre méthode par continuation a permis de brancher une implémentation asynchrone de notre service.</strong></p>

<p style="text-align: justify;">(On peux alors regarder le gars du fond de la salle et lui faire un petit hochement de tête complice!)</p>

<h2 style="text-align: justify;">A voir aussi</h2>

<ul style="text-align: justify;">
    <li><a href="http://www.arolla.fr/blog/2012/09/la-gestion-des-erreurs-avec-scala-util-try-12/">La gestion des erreurs avec scala.util.Try (1/2) - Nouhoum</a></li>
    <li><a href="http://www.arolla.fr/blog/2012/10/la-gestion-des-erreurs-avec-scala-util-try-22/">La gestion des erreurs avec scala.util.Try (2/2) - Nouhoum</a></li>
    <li><a href="http://blog.richdougherty.com/2012/06/error-handling-with-scalas-try.html">Error handling with Scala's Try</a></li>
</ul>

<ul>
    <li style="text-align: justify;"><tt>Either</tt>
<ul>
    <li>Either ~ Haskell</li>
    <li><a href="http://www.functionaljava.org/">Either ~ FunctionalJava</a></li>
    <li>Either ~ Scala</li>
    <li><a href="http://msdn.microsoft.com/en-us/library/ee353439.aspx">Choice ~ F#</a></li>
</ul>
</li>
    <li><tt>Option</tt>
<ul>
    <li style="text-align: justify;">Maybe ~ Haskell</li>
    <li style="text-align: justify;"><a href="http://www.functionaljava.org/">Option ~ FunctionalJava</a></li>
    <li style="text-align: justify;"><a href="http://www.scala-lang.org/api/current/scala/Option.html">Option ~ Scala</a></li>
    <li style="text-align: justify;"><a href="http://msdn.microsoft.com/en-us/library/ee353806.aspx">Option ~ F#</a></li>
</ul>
</li>
</ul>
