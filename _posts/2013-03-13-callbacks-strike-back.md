---
layout: post
title: Callbacks strike Back
date: 2013-03-13 11:56
author: arnauld loyer
comments: true
categories: [callback, continuation, deferred, Fonctionnel, java, javascript, nodeJS, non-blocking, Programmation, programming, promise]
---
<a href="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-3.jpg"><img class="alignright size-full wp-image-1472" src="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-3.jpg" alt="" width="320" height="490" /></a>

Cet article se place dans la continuité de l'article précédent: <a title="Il y a peut être une option pour continuer ¡¿ (réflexion sur la programmation par continuation)" href="http://www.arolla.fr/blog/2012/12/option-maybe-continuation/" target="_blank">Il y a peut être une option pour continuer ¡¿.</a>
Avant de présenter de nouvelles techniques - les <em>promises</em> / <em>deferred</em> / <em>future</em> - nous commencerons par transposer les techniques vues précédement en javascript. En poussant le bouchon un peu plus loin, nous verrons les limites de <a href="http://www.youtube.com/watch?v=ASc40_mpZjU">Maurice</a> et comment, à force de promesses, des alternatives se proposent. A l'issu de cet article TL;TR, les fonctions de rappel ne devraient plus avoir de secret pour vous!

<h2>Preambule: Javascript ça <code>function-ne</code> pas mal!</h2>

L'une des richesses de Javascript par rapport à d'autres langages (comme "Java") est que la <code>function</code> est <span style="color: #b80f07">un objet de première classe</span>: <strong>Une fonction est un objet et il est possible de l'affecter à une variable</strong>.

[javascript]
var add = function(a,b) { return a + b; }
[/javascript]

Il est même possible de définir des fonctions qui renvoient d'autres fonctions, on parle alors de fonction d'ordre supérieur:

[javascript]
var adder = function(a) {
  return function(b) {
    return add(a, b);
  }
};

var add7 = adder(7);
assert(add7(0) === 7);
assert(add2(35) === 42);
[/javascript]

Enfin, on peux même définir des fonctions qui prennent d'autres fonctions en paramètres et renvoient des fonctions:

[javascript]
var curry = function(func, a) {
  return function(b) {
    return func(a,b);
  }
}

var add7 = curry(add, 7);
assert(add7(0) === 7);
assert(add7(35) === 42);
[/javascript]

Un autre exemple, que l'on rencontre sans doute plus fréquement, en <code>jquery</code> :

[javascript]
$(&amp;amp;amp;amp;amp;amp;quot;#login&amp;amp;amp;amp;amp;amp;quot;).on('click', function (event) {
    $(&amp;amp;amp;amp;amp;amp;quot;#login-pane&amp;amp;amp;amp;amp;amp;quot;).show();
});
[/javascript]

Une fonction de rappel est invoquée lorsqu'un clic est détecté sur l'élément <code>login</code>; elle affiche alors l'élément <code>login-pane</code>.

Notes:

<ul>
    <li>J'invite le lecteur à lire l'excellent article: <a title="Les fonctions et les fonctions d’ordre supérieur" href="http://www.arolla.fr/blog/2013/01/les-fonctions-et-les-fonctions-d%e2%80%99ordre-superieur/">Les fonctions et les fonctions d'ordres supérieur</a> si ce n'est pas déjà fait.</li>
    <li><a href="http://fr.wikipedia.org/wiki/Fonction_d%27ordre_sup%C3%A9rieur">Fonction d'ordre supérieur</a></li>
    <li><a href="http://fr.wikipedia.org/wiki/Curryfication">Curryfication</a></li>
</ul>

<h2>Les techniques vu précédemment</h2>

<blockquote>
 <span style="color: #b80f07">Première technique</span> :

 <strong>Ajouter une fonction supplémentaire comme paramètre lors de l'appel d'une méthode. Cette fonction pourra alors être appellée avec le résultat du calcul lorsque celui sera disponible.</strong></blockquote>

Reprenons le code Java de notre première technique et transposons le en <strong>un</strong> équivalent javascript:

[java]
public class QuizService {
 ...
 public void create(String quizContent, Effect&amp;amp;amp;amp;amp;amp;lt;Quiz&amp;amp;amp;amp;amp;amp;gt; effect) {
 Quiz quiz = quizFactory.create(nextId(), quizContent);
 effect.e(quiz);
 }
}
[/java]

devient:

[javascript]
QuizService.prototype.create = function(quizContent, callback)
{
  var factory = this.quizFactory;
  var quiz = factory.create(this.nextId(), quizContent);
  callback(quiz);
}
[/javascript]

La transposition est relativement claire. Illustrons cela par un exemple d'appel:

[javascript]
quizService.create(&amp;amp;amp;amp;amp;amp;quot;&amp;amp;amp;amp;amp;amp;lt;question4aChampion&amp;amp;amp;amp;amp;amp;gt;...&amp;amp;amp;amp;amp;amp;quot;, function(quiz) {
  displayQuiz(quiz);
});
[/javascript]

Une fois le quiz créé, il est passé en paramètre à notre fonction qui se contente de demander son affichage. Comme les arguments sont identiques cela peux même se réduire à (n'oubliez pas qu'une fonction est un objet de première classe):

[javascript]
quizService.create(&amp;amp;amp;amp;amp;amp;quot;&amp;amp;amp;amp;amp;amp;lt;question4aChampion&amp;amp;amp;amp;amp;amp;gt;...&amp;amp;amp;amp;amp;amp;quot;, displayQuiz);
[/javascript]

Passons rapidement à la deuxième technique:</pre>

<blockquote>
 <span style="color: #b80f07">Deuxième technique</span> :

 <strong>La fonction de rappel est définie comme prenant un résultat dont le type peut varier en fonction du déroulement du calcul...</strong></blockquote>

[java]
public class QuizService {
  public void create(String quizContent,
                     Effect&amp;amp;amp;amp;amp;amp;lt;Either&amp;amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;amp;gt;&amp;amp;amp;amp;amp;amp;gt; effect) {
    if(quizIsUnique(quizContent)) {
      Quiz quiz = quizFactory.create(nextId(), quizContent);
      Either&amp;amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;amp;gt; left = Eithers.left(quiz);
      effect.e(left);
    }
    else {
      // Failure is a Pojo but could
      // also be an UniqueConstraintException
      Failure failure = new Failure(Code.NonUniqueQuiz);
      Either&amp;amp;amp;amp;amp;amp;lt;Quiz,Failure&amp;amp;amp;amp;amp;amp;gt; right = Eithers.right(failure);
      effect.e(right);
    }
  }
}
[/java]

Là, les choses vont commencer à se simplifier:
[javascript]
QuizService.prototype.create = function(quizContent, callback)
{
  if(this.quizIsUnique(quizContent)) {
    var factory = this.quizFactory;
    var quiz = factory.create(this.nextId(), quizContent);
    callback(null, quiz);
    // or callback.apply(null, [null, quiz]);
  }
  else {
    var failure = new Failure(Code.NonUniqueQuiz);
    callback(failure);
    // or callback.apply(null, [failure]);
  }
}
[/javascript]

<div style="margin-left: 1em;margin-bottom: 1em;padding-left: 0.5em;font-style: italic;border: 1px solid #e5e5e5;background: #f8f8f8">
<h4>Pour aller plus loin: le Rituel d'invocation</h4>

<strong>du direct, un peu d'<code>apply</code>, un zeste de <code>call</code> et une pincée de <code>this</code></strong> Il existe plusieurs façon d'invoquer une fonction lorsque l'on dispose d'une reference sur celle-ci. Il est possible de l'invoquer directement lorsque l'on connait les arguments exacts auxquels elle s'attend: <code>callback(null, quiz)</code>. Dans ce cas tout se passe bien tant que l'on ne se soucie guère de la notion de <code>this</code>. L'instance référencée par <code>this</code> au moment de l'execution du callback est alors non maitrisé, ce qui dans la plupart des cas ne pose pas de réel problème tant qu'on ne l'utilise pas. En revanche quand on est amené à utiliser le <code>this</code> il devient alors indispensable de connaitre ce qu'il référence. Ce qui est typiquement le cas en JQuery: 

[javascript]
$(&amp;amp;amp;amp;amp;amp;quot;.button&amp;amp;amp;amp;amp;amp;quot;).click(function() {
 var buttonId = $(this).attr(&amp;amp;amp;amp;amp;amp;quot;id&amp;amp;amp;amp;amp;amp;quot;);
 console.log( &amp;amp;amp;amp;amp;amp;quot;Button clicked: &amp;amp;amp;amp;amp;amp;quot; + buttonId);
});
[/javascript] 

Ligne 2, le <code>this</code> référence l'élément qui a été cliqué. C'est JQuery qui se charge d'invoquer la fonction de rappel en lui associant en <code>this</code> l'élément qui vient d'être cliqué. Cette association se fait par l'intermédiaire des fonctions <code>apply</code> et <code>call</code> (<strong>Eh oui une fonction est un objet à part entière et dispose elle-même de fonctions!</strong>). 

Il existe enfin une petite subtilité, en définissant le <code>this</code> on ne définit pas seulement la valeur d'une variable, mais aussi l'objet sur lequel la fonction est invoquée. Les plus curieux pourront donc consulter les articles <em>"8.7.3 The call() and apply() Methods - Javascript: The Definitive Guide"</em> et <a href="https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Function/apply">Function.prototype.apply method</a> pour plus d'informations sur les subtilités de ces fonctions. On pourra aussi consulter (<em>8.3.2 Variable-Length Argument Lists: The Arguments Object - Javascript: The Definitive Guide</em>) sur leur usages conjointement avec la variable spéciale <code>arguments</code>, ce qui permet de traiter efficacement les fonctions dont le nombre de paramètres peut varier d'une invocation à l'autre.</div>

Qu'avons-nous fait? Eh bien notre fonction de rappel peut prendre deux paramètres: le premier, s'il est non <code>nul</code> (voir aussi <a href="http://oreilly.com/javascript/excerpts/javascript-good-parts/awful-parts.html#the_many_falsy_values_of_javascript">Falsy values in javascript</a>) désigne une erreur, tandis que le second désigne le résultat en cas de succès.
Par convention, on place généralement l'erreur comme premier paramètre.

Voyons alors le code appellant:

[javascript]
quizService(&amp;amp;amp;amp;amp;amp;quot;...&amp;amp;amp;amp;amp;amp;quot;, function(err, quiz) {
  if(err) {
    displayErrorFeedback(err);
  }
  else {
    displyFlashFeedback(quiz);
  }
})
[/javascript]

Allez hop, on enchaine avec la troisième technique:</pre>

<blockquote>
 <span style="color: #b80f07">Troisième technique</span> :

 <strong>La fonction de rappel est définie comme prenant un résultat dont le contenu est optionnel</strong></blockquote>

Avant:

[java]
public class QuizService {
  public void save(Quiz quiz, Effect&amp;amp;amp;amp;amp;amp;amp;lt;Option&amp;amp;amp;amp;amp;amp;amp;gt; effect) {
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

Après:

[javascript]
QuizService.prototype.save = function(quiz, callback) {
  try {
    this.repository.save(quiz);
    callback();
  }
  catch(e) {
    callback(e);
  }
}
[/javascript]

Bon... en pratique on verra (ou ecrira) très rarement du code comme ça! mais plutôt:

[javascript]
QuizService.prototype.save = function(quiz, callback) {
  this.repository.save(quiz, function(err,res) {
    callback(err);
  });
}
[/javascript]

ou même beaucoup plus simplement:

[javascript]
QuizService.prototype.save = function(quiz, callback) {
  this.repository.save(quiz, callback);
}
[/javascript]

Illustrons cela par un exemple d'utilisation:

[javascript]
quizService.save(quiz, function(err) {
  if(typeof err !== &amp;amp;amp;amp;amp;amp;quot;undefined&amp;amp;amp;amp;amp;amp;quot;) {
    displayErrorFeedback(err);
  }
  else {
    displayFlashFeedback(Code.QuizSaved);
  }
}
});
[/javascript]

<a href="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image.jpg"><img class="alignleft size-full wp-image-1473" src="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image.jpg" alt="" width="320" height="490" /></a>

Que pouvons-nous constater? Qu'il est possible d'invoquer la même fonction javascript avec ou sans un nombre variable de paramètres. C'est à la charge de la fonction invoquée de vérifier le nombre de paramètres passés, éventuellement leurs types, afin de choisir le comportement qu'elle doit adopter.
Dans notre cas, on vérifie l'existence d'un paramètre qui, le cas échéant, sera référencé par la variable <code>err</code>. En l'absence de paramètre, cette variable sera considérée comme indéfinie.

Pourquoi ne pas utiliser <code>if(err)</code> puisque <code>undefined</code> est évaluée comme <code>false</code> (rappel: <a href="http://oreilly.com/javascript/excerpts/javascript-good-parts/awful-parts.html#the_many_falsy_values_of_javascript">Falsy values in javascript</a>)? Uniquement au cas où l'instance décrivant l'erreur serait évaluée à false (se serait maladroit mais bon... sait-on jamais). Les tatillons pourraient dire: "mais pourquoi tout à l'heure on ne s'est pas préoccupé de ça?" hummm... et bien tout simplement parce qu'il faut y aller petit à petit et donner différentes techniques au fur et à mesure.

Passons très vite sur la quatrième technique:

<blockquote><span style="color: #b80f07">Quatrième technique</span> :

<strong>La fonction de rappel est définie comme une fonction ne prenant aucun paramètre, elle est invoquée pour signaler que l'action désirée est effectuée</strong></blockquote>

[javascript]
QuizService.prototype.save = function(quiz, onceSaved) {
  this.repository.save(quiz, function(err,res) {
    onceSaved();
  });
}
[/javascript]

<h2>Et pourquoi parler de Javascript maintenant?</h2>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-1.jpg"><img class="alignright size-full wp-image-1470" src="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-1.jpg" alt="" width="332" height="508" /></a>

Eh bien parce qu'il devient difficile de ne pas voir que son utilisation devient de plus en plus présente avec des interfaces utilisateur de plus en plus riches. Il suffit de voir l'approche RIA et son nombre croissant de framework: Backbone.js, AngularJS, Ember.js, KnockoutJS, ... et même Batman.js! (voir <a href="http://todomvc.com/">Todo MVC</a> pour la comparaison d'une TODO list réalisée avec les différents frameworks).
Et malgré ce que peuvent en dire certains, sa présence côté serveur - popularisée par la plateforme NodeJs -a de quoi nous interpeller. D'ailleurs une grande partie du succès de NodeJs réside dans son approche non bloquante, et toute son API est tournée autour des techniques que nous venons de voir: des appels asynchrones auxquels on passe des fonctions de rappel.

<h2>Reveille le Numérobis qui sommeille en toi!</h2>

Voyons à quoi ressemblerait une application prenant en compte à chaque appel cette notion de fonction de rappel:

[javascript]
checkUniqueness(data, function(err) {
  if(err)
    displayError(err);
  else
    createQuiz(data, function(err, quiz) {
      if(err)
        displayError(err);
      else
        saveQuiz(quiz, function(err, quizId) {
          if(err)
            displayError(err);
          else
            lookupQuiz(quizId, function(err, quiz) {
              if(err)
                displayError(err);
              else
                sendQuiz(quiz)
            })
        })
    })
})
[/javascript]

Mouais... c'est un bien bel escalier qui se dessine; certains parlnte même de pyramide funeste:

<blockquote>As we all know, asynchronous I/O leads to callback API’s, which lead to nested lambdas, which lead to... the pyramid of doom

Why coroutines won't work on the web</blockquote>

Le code devient difficile à lire, à comprendre et par conséquent à maintenir et à faire évoluer!

Pour la petite histoire, c'est justement en arrivant à ce constat en écrivant le code implémentant les différents motifs vus dans <a href="http://www.arolla.fr/blog/2012/11/amqp-101-part-1/">AMQP 101 - Part 1</a> que j'ai cherché une alternative plus élégante pour écrire la même fonctionalité. Il me fallait trouver une manière plus lisible d'écrire et donc de comprendre les exemples, tout ça pour toi cher lecteur! (On vous bichone bien non?)

<h2>Des promesses, des promesses et encore des promesses</h2>

Que ce cache sous ce titre? Et bien une nouvelle technique: les <code>Promises</code>

Voyons tout d'abord le résultat auquel on souhaite arriver en reprenant l'example précédent:

[javascript]
checkUniqueness(data)
  .then(displayError, function(data) {
    return createQuiz(data);
  })
  .then(displayError, function(quiz) {
    return saveQuiz(quiz);
  })
  .then(displayError, function(quizId) {
    return lookupQuiz(quizId);
  })
  .then(displayError, sendQuiz);
[/javascript]

que l'on peut alléger en (souvenez-vous: une fonction est un objet de première classe - hummm je radote là?):

[javascript]
checkUniqueness(data)
  .then(displayError, createQuiz)
  .then(displayError, saveQuiz)
  .then(displayError, lookupQuiz)
  .then(displayError, sendQuiz);
[/javascript]

On retrouve alors notre enchainement d'appels, mais cette fois chaque appel est lié au précédent via la méthode <code>then</code>. Cette methode enregistre alors deux fonctions de rappel: une pour le traitement d'erreur et une pour le traitement du résultat.
Ces fonctions de rappel seront alors invoquées lorsque l'appel précédent sera terminé en fonction de son échec ou de son résultat. L'erreur ou le résultat est alors automatiquement transmis en paramètre aux fonctions de rappel enregistrées.

Il s'agit là du principe des <code>Promises</code> en gros: tu m'invoques, je te renvoie un accusé de reception sur lequel tu peux t'enregistrer, quand j'aurai fini mon traitement je te promets que je te passe son résultat.

L'interêt est alors de pouvoir enchainer ces <code>promises</code> et ainsi de décrire tout l'enchainement du traitement qui devra être effectué à mesure que les résultats passent d'une étape à l'autre. Le découplage entre les étapes apparait clairement, et il devient plus facile d'insérer ou de modifier des étapes.

Afin de faire apparaitre les <code>promises</code>, le code précédent pourrait être réécrit de la manière suivante (il s'agit strictement du même code, la seule différence est de faire apparaitre explicitement les variables intermédiaires au lieu de chainer les appels directement):

[javascript]
var promise0 = checkUniqueness(data);
var promise1 = promise0.then(displayError, function(data) {
                  return createQuiz(data);
                });
var promise2 = promise1.then(displayError, function(quiz) {
                  return saveQuiz(quiz);
                });
var promise3 = promise2.then(displayError, function(quizId) {
                  return lookupQuiz(quizId);
                });
promise3.then(displayError, sendQuiz);
[/javascript]

A quoi pourrait ressembler une implémentation très <strong>simpliste</strong> (voir <a href="https://github.com/kriskowal/q">q</a> pour une implémentation plus complète et beaucoup plus robuste):

[javascript]
var Promise = function () {
  this.resolved = false;
  this.callbacks = [];
};

Promise.prototype.resolve = function(error, data) {
	this.data = data;
  this.error = error;
	this.callbacks.forEach(function(cb) {
    if(error)
      cb[0](error);
    else
      cb[1](data);
  });
	this.callbacks = [];
	this.resolved = true;
}

Promise.prototype.then = function(errCallback, okCallback) {
	if(this.resolved) {
        if(this.error)
          errCallback(this.error);
        else
          okCallback(this.data);
    }
    else {
    	this.callbacks.push([errCallback, okCallback]);
	}
}
[/javascript]

Ok... et comment on transforme nos méthodes précédentes pour intégrer cela? Et bien cela nécessite d'adapter légèrement nos API afin qu'elles intègrent directement les <code>Promises</code>:

[javascript]
var checkUniqueness = function(data) {
  var promise = new Promise();
  db.checkUniqueness(function(error) {
    promise.resolve(error, data);
  });
  return promise;
}

var createQuiz = function(data) {
  var promise = new Promise();
  service.createQuiz(data, function(error, quiz) {
    promise.resolve(error, quiz);
  });
  return promise;
}

...
[/javascript]

Biensûr tout cela a encore plus d'interêt lorsque les appels <code>db.checkUniqueness(...)</code> et <code>service.createQuiz(...)</code> sont asynchrones:
Les deux méthodes précédentes sont invoquées et avant même que le résultat ne soit disponible la <code>promise</code> correspondante est retournée afin de continuer à définir le traitement à effectuer. La chaine de traitement est définie en même temps que la base de données ou que notre service travaille.

Quelques explications?

<code>checkUniqueness</code> est invoquée avec les données à vérifier <code>data</code>. Une <code>promise</code> est instancié spécialement pour l'occasion afin d'être notifiée lorsque le traitement sera terminé. La vérification d'unicité est alors déléguée à la base de donnée (fallait bien trouver un responsable!) et en attendant son verdict, la <code>promise</code>est retournée telle quelle.
Il est alors possible d'enregistrer plusieurs <code>callback</code> dessus en utilisant la méthode <code>then(cbErr,cbOut)</code> (exemple: <code>.then(displayError, function() { return createQuiz(data); })</code>).
Les <code>callback</code> ainsi enregistrés seront alors notifiés dès que le résultat sera disponible, c'est à dire lorsque la méthode <code>promise.resolve(err,out)</code> est invoquée avec le résultat du traitement.

Le public averti pourra s'appercevoir qu'une <code>promise</code> est - elle-même - basée sur le mécanisme de callback et se place elle-même en fonction de rappel sur le traitement effectué.

L'exemple précédent nous a montré comment transformer des appels imbriqués en des déclarations séquentielles.
Exploitons désormais quelques possibilités offertes par les librairies autour de ces promises (nous baserons notre discours sur la librairie <a href="https://github.com/kriskowal/q">q</a>).

Voyons comment nous pouvons étendre les mêmes techniques à des traitements effectués en parallèle, et plus particulièrement ceux nécessitant des points de "synchronisation": une fois que mes sous-tâches sont terminées alors seulement la suite de mon traitement peut continuer.

Par exemple si nous souhaitons générer tout un recueil de quiz:

Commençons par définir une fonction utilitaire de création de quiz, regroupant ce que nous avons pu voir jusqu'à présent. La fonction retourne une <code>promise</code> sur la création du quiz:

[javascript]
function generateQuiz(errorHandler) {
  var promise =
        generateData()
          .then(errorHandler, checkUniqueness)
          .then(errorHandler, createQuiz)
          .then(errorHandler, saveQuiz);
  return promise;
}
[/javascript]

Une fonction utilitaire qui génèrera <code>nbQuiz</code> en invoquant la fonction précédente:

[javascript]
function generateBook(nbQuiz, errorHandler) {
  var i,
      promises = [];
  for(i=0; i &amp;amp;amp;amp;amp;amp;lt; nbQuiz; i++) {
    var promise = generateQuiz(errorHandler);
    promises.push(promise);
  }

  return return Q.all(promises);
}
[/javascript]

Le retour de notre méthode <code>generateBook</code> est une <code>promise</code>. Les fonctions de rappels qui seront "branchées" dessus seront alors invoquées lorsque les <code>nbQuiz</code> questionnaires auront été générés, créés et enregistrés.

[javascript]
generateBook(25, errorHandler)
  .then(errorHandler, generateTableOfContent)
  .then(errorHandler, sendToFrance3)
  ...;
[/javascript]

En continuant de creuser, on peux trouver beaucoup d'autres techniques. On s'apperçoit que cela ouvre énormément de possibilités quand à la gestion de tâches asynchrones, éventuellement concurrentes, et ce de manière lisible.

<h2>Qui est concerné?</h2>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-2.jpg"><img class="alignright size-full wp-image-1471" src="http://www.arolla.fr/blog/wp-content/uploads/2013/02/Pulp-O-Mizer_Cover_Image-2.jpg" alt="" width="332" height="508" /></a>

Suis-je concerné? Je pensais que le Javascript était executé par un seul fil d'execution et là on me parle de traitement asynchrone ?

<blockquote><strong>One of the fundamental features of client-side JavaScript is that it is single-threaded</strong>: a browser will never run two event handlers at the same time, and it will never trigger a timer while an event handler is running, for example. <strong>Concurrent updates to application state or to the document are simply not possible, and client-side programmers do not need to think about, or even understand, concurrent programming</strong>.

Javascript The Definitive Guide 6th Ed - 22.4 WebWorkers</blockquote>

Et en quoi cela nous concerne-t-il si l'on développe sur le navigateur?

Eh bien parce qu'il n'est pas toujours question de traitement effectué sur le navigateur, mais il peut aussi s'agir d'interaction avec des systèmes tierces comme une requête AJAX:

<blockquote>A corollary is that client-side JavaScript functions must not run too long: otherwise they will tie up the event loop and the web browser will become unresponsive to user input. <strong>This is the reason that Ajax APIs are always asynchronous and the reason that client-side JavaScript cannot have a simple, synchronous load() or require() function for loading JavaScript libraries.</strong>

Javascript The Definitive Guide 6th Ed - 22.4 WebWorkers</blockquote>

<blockquote><strong>Everything</strong> except network operations happens in a single thread

<a href="https://speakerdeck.com/dmosher/so-you-want-to-be-a-front-end-engineer">So, You Want to Be a Front-End Engineer?</a></blockquote>

Les bibliothèques JQuery et Dojo fournissent d'ailleurs des API selon le modèle des <code>promises</code> appellé chez eux <code>deferred</code> (<a href="http://dojotoolkit.org/reference-guide/1.7/dojo/Deferred.html">Dojo ~ Deferreds</a> et JQuery ~ Deferreds).

De plus, comme l'auront noté les petits malins, HTML5 définit la notion des <code>WebWorkers</code> qui permet d'effectuer des traitements en dehors du fil d'execution Javascript de la page. Il est donc tout à fait possible d'effectuer des traitements asynchrones même sur un navigateur en les déléguant à des WebWorkers (Nous reviendrons sans doute sur eux dans un prochain article).

Quand à la plateforme NodeJs, et bien, elle intègre par construction la nature asynchrone de chaque appel IO (Input/Output). Les spécifications sur lesquelles s'appuient la plateforme prévoit même une API unifiée et standard pour les promises (CommonJS ~ Promises/A pour lequel <a href="https://github.com/kriskowal/q">q</a> est compatible).

Et pour ceux qui <strong>jamais au grand jamais</strong> ne toucheront à du javascript!?

Et bien, tout d'abord, vous avez tort de ne pas vous y interesser. Ensuite, ces notions non-bloquantes deviennent de plus en plus présentes et l'on retrouve tous ces concepts dans les langages intégrant les notions de <a href="http://savanne.be/articles/concurrency-in-erlang-scala/">process/acteur</a> (erlang et scala dont l'excellente librairie <a href="http://akka.io">akka</a>) et du coup dans les frameworks comme <a href="http://www.playframework.com/documentation/2.1.0/ScalaAsync">Play!</a> ou <a href="https://mvnrepository.com/artifact/org.codehaus.gpars/gpars">GPars</a>.

<h2>Webographie</h2>

Une très belle présentation:

<ul>
    <li><a href="http://news.humancoders.com/t/prog-fonctionnelle/items/2584-futures-et-promesses-en-scala-2-10"> Futures et Promesses en Scala 2.10 (en)</a></li>
</ul>

<ul>
    <li><a href="http://fr.wikipedia.org/wiki/Fonction_d%27ordre_sup%C3%A9rieur">Fonction d'ordre supérieur</a></li>
    <li><a href="http://fr.wikipedia.org/wiki/Curryfication">Curryfication</a></li>
</ul>

<ul>
    <li><a href="https://github.com/kriskowal/q">kriskowal / q</a> : A tool for making and composing asynchronous promises in JavaScript</li>
</ul>

<ul>
    <li><a href="http://www.infoq.com/articles/surviving-asynchronous-programming-in-javascript">"How to Survive Asynchronous Programming in JavaScript" on InfoQ</a></li>
</ul>

<ul>
    <li>CommonJS ~ Promises/A</li>
    <li><a href="http://dojotoolkit.org/reference-guide/1.7/dojo/Deferred.html">Dojo ~ Deferreds</a></li>
    <li>JQuery ~ Deferreds</li>
    <li><a href="http://joseoncode.com/2011/09/26/a-walkthrough-jquery-deferred-and-promise/">Understanding JQuery.Deferred and Promise</a></li>
    <li><a href="http://spin.atomicobject.com/2012/03/14/nodejs-and-asynchronous-programming-with-promises/">Node.js and Asynchronous Programming with Promises</a></li>
</ul>

<ul>
    <li><a href="http://todomvc.com/">Todo MVC</a></li>
</ul>

<ul>
    <li><a href="http://akka.io">Akka</a></li>
    <li>Play!</li>
</ul>

Livres

<ul>
    <li><a href="http://shop.oreilly.com/product/9780596805531.do">JavaScript: The Definitive Guide</a></li>
    <li><a href="http://shop.oreilly.com/product/9780596517748.do">JavaScript: The Good Parts</a></li>
</ul>
