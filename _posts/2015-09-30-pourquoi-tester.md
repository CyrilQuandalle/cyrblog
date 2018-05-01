---
layout: post
title: Pourquoi tester ?
date: 2015-09-30 10:28
author: fabien maury
comments: true
categories: [Bonnes pratiques de dév, clean code, craftsmanship, développement, Programmation, quality, TDD, testing]
---
Cet article va parler de tests, et donc de code.

Parler de code est assez vague, car nous ne faisons pas tous exactement le même métier.

Que l'on développe un algorithme de compression vidéo, une application métier ou le noyau linux, nos besoins sont différents (bien que je reste persuadé que nous avons tous besoin de tests).
Il est donc nécessaire de prendre du recul sur cet article, car mon point de vue sera forcément orienté et ne correspondra peut-être pas à votre situation.

<h2>De quoi parlons-nous ?</h2>

Ici je vais vous parler des tests dans mon quotidien, qui ressemble peut-être au vôtre:

<strong>Je suis un prestataire</strong>, j'arrive donc sur des projets dans diverses entreprises, sur des durées plus ou moins longues.
<strong>Mon code sera donc repris par quelqu'un d'autre.</strong>

<strong>Je travaille sur des applications métier</strong>. Les règles que je vais devoir implémenter sont dictées par des personnes et ces règles:

<ul>
<li>Peuvent être ambiguës</p></li>
<li><p>Peuvent être contradictoires entre elles</p></li>
<li><p><del datetime="2015-09-30T05:20:41+00:00">Peuvent</del> Vont changer</p></li>
</ul>

<p>Ce qui est normal car nous rentrons dans le domaine de l'humain.
Ce type de code est le code le plus vivant que l'on puisse trouver, il va <strong>évoluer régulièrement</strong>, et va donc ensuite <strong>devoir être refactoré</strong> afin de correspondre à la meilleure implémentation possible pour cette situation.

Si un code n'est pas testé, il ne peut être refactoré (ce qui arrive dans les lieux où la phrase "never touch a running system" devient loi et où l'on copie colle le code gaiement par peur de modifier l'ancien).

<blockquote>
  Pourquoi tester ? <strong>Pour que le code soit refactorable, sinon nous avons écrit du code Legacy</strong>
</blockquote>

<h2>Bob reprend mon code</h2>

Prenons l'exemple de Bob, qui arrive après moi sur le projet, et doit ajouter une règle métier.

Bob rajoute donc un test qui vérifie la règle (le test est en erreur pour l'instant), puis implémente la règle. Le test passe au vert (oui, Bob fait du Test Driven Development !) mais juste avant de commiter, au moment du build (décidément Bob est un bon gars, car il joue les tests avant d'envoyer son code !) un ancien test ne passe plus.

<strong>Premier constat</strong>: Un test existe (je n'aurais effectué qu'un test manuel lors de mon développement pour vérifier mon code, Bob n'aurait pas détecté cette régression).

<blockquote>
  Pourquoi tester ? <strong>Pour vérifier que l'implémentation restera conforme à la règle attendue.</strong>
</blockquote>

Bob se pose donc la même question que tout le monde à cette étape: Doit-il <strong>modifier le test</strong> ou <strong>modifier son implémentation</strong> ?

Si le test en question ressemble à celui-ci:

[java]
public void testUserCreation(){
   //TODO imaginer ici 40 lignes mélangeant pêle-mêle des appels,
   //des initialisations de mocks et des assertions diverses
}
[/java]

Alors Bob finira sans doute, après avoir tenté de comprendre l'intention pendant 15mn, par modifier le test pour qu'il passe.

<blockquote>
  Pourquoi tester ? <strong>Parce que vos tests servent de documentation</strong>
</blockquote>

<h4>Comment savoir si le test est encore d'actualité ?</h4>

1) <strong><em>Des noms de test explicites</em></strong>

Un nom comme <code>public void should_send_confirmation_sms_when_created_user_have_phone_number</code> permet d'énoncer clairement la règle testée ici et oblige à découper vos tests par règle de gestion.

2) <strong><em>Des tests lisibles</em></strong>

Bien séparer l'initialisation, l'action testée et les assertions rendra le test plus lisible. Cela peut aller du saut de ligne à l'utilisation de la syntaxe Gherkin...en passant par des builders.

Il est alors facile pour Bob d'identifier si cette règle est encore d'actualité, ou d'aller se renseigner si cette règle précise est bien compromise par la nouvelle. Il se peut aussi que l'on ait mis le doigt sur une incompatibilité entre règles.

<blockquote>
  <strong>Le plus souvent, nous sommes notre propre Bob, mais 3 mois plus tard. Penser à Bob, c'est penser à soi.</strong>
</blockquote>

Après avoir statué sur ce test, Bob va pouvoir faire passer tous les tests en connaissance de cause, puis refactorer (pour introduire un pattern plus adéquat, extraire du code, etc).
A force de refactoring, et au fil du temps, le code initial que j'avais écrit disparaîtra en grande partie pour mieux s'adapter aux nouvelles contraintes, ou juste pour mieux correspondre à la vision du nouveau développeur.

<h2>Mais encore...</h2>

Ce sont pour moi des raisons suffisantes, cependant il y a d'autres façon d'aborder ce "pourquoi". Il est aujourd'hui communément admis qu'il est "plus propre" de tester, mais beaucoup de personnes testent sans plus vraiment se demander pourquoi au risque de perdre son but et dénaturer l'intention première.

Écrire un test est donc une bonne chose, mais encore faut-il qu'il soit écrit pour être utile et la seule façon d'y arriver est de se poser les bonnes questions.
Cela va déjà nous permettre de savoir le type de test qu'il nous faut.
Je souhaite tester:

<ul>
<li>que mon validateur prenne en compte tous les cas métiers de façon exhaustive: plusieurs tests unitaires</p></li>
<li><p>que mon validateur utilise correctement tel service externe de référentiel: un test d'intégration</p></li>
<li><p>que mon validateur est bien appelé lors de la validation du formulaire: un test end-to-end</p></li>
</ul>

<h3>On ne se mock pas</h3>

<p>Quelle que soit le projet sur lequel j'interviens, je trouve une multitude de tests unitaires. Cependant il est très fréquent que je rencontre des tests de qualité discutable:

<ul>
<li>un gros test par méthode</p></li>
<li><p>des lignes à n'en plus finir d'initialisation de mocks</p></li>
<li><p>certaines fois sans assertions (bien que ce soit de plus en plus rare)</p></li>
</ul>

<p>Je suis un grand fan des bibliothèques de mocks, tel Mockito. Cependant leur travers est que nous avons tendance à tester des détails d'implémentation. Le code étant ainsi figé, transformant chaque modification du code en séance de réécriture des tests existants.
Ce type de test a été écrit en s'égarant du but premier:

Pourquoi j'écris ce test ? Pour vérifier que j'envoie un mail lorsque l'utilisateur s'inscrit à un service nécessitant une confirmation d'adresse mail.

Je souhaite donc juste tester que si le service nécessite une confirmation d'email, mon système souhaite envoyer un message de type "confirmation d'email".
Si votre test utilise des mocks pour simuler un templateMailRenderer, des classes du package javax.mail et autres dépendances de plus bas niveau, alors votre test aura tendance à tester un détail d'implémentation,et non un comportement (behavior).

<blockquote>
  <strong>On mock des comportements, pas de l’interaction avec de la data</strong>
</blockquote>

Je vous recommande sur ce sujet <a href="http://martinfowler.com/bliki/TellDontAsk.html" target="_blank">l'article de Martin Fowler "tell,dont ask".</a>

Mais attention, écrire un test utile, qui se place au bon niveau d'abstraction et vérifie le comportement attendu plutôt qu'un détail d'implémentation n'est pas chose aisée, et il n'y a pas de recette miracle.
Il ne s'agit donc pas de jeter la pierre, mais de poser celle qui permettra peut-être à certains d'entre vous de prendre quelques secondes avant d'écrire un test, afin d'optimiser son utilité.

Et surtout, gardez toujours à l'esprit que derrière des problèmes de test se cachent souvent des problèmes de design.

<h2>Les tests, nos vrais livrables ?</h2>

On peut remarquer que du code écrit à un instant t, le code de production ne persistera plus qu'en partie, alors que la plupart des tests seront encore d'actualité.

C'est pour cette raison que je préfère souvent considérer que je livre à mon client des tests, et non du code de production.
Ce dernier n'étant, au final, qu'un détail d'implémentation des règles de gestion demandées par le client.

Donc je conclurai par: pourquoi tester ? Parce que votre code de test a plus de chances de survivre que votre code de production !
