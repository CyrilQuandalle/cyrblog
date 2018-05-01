---
layout: post
title: "Catch-Exception : pour tester vos exceptions sur JUnit"
date: 2014-03-05 12:00
author: yakhya dabo
comments: true
categories: [Agilité, Bonnes pratiques de dév, java, junit, Outils, Programmation, TDD]
---
<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/03/Clean.jpg"><img class="size-medium wp-image-2235 alignleft" alt="Clean" src="http://www.arolla.fr/blog/wp-content/uploads/2014/03/Clean-300x192.jpg" width="300" height="192" /></a>L'objectif principal des tests est de garantir la qualité du code de production en permettant des feed back rapides au moment du Refactoring. Il est malheureusement très courant de tomber sur du code de test sale, très sale, et des tests mal faits. L'une des situations où l'on peut rencontrer des problèmes de lisibilité c'est quand on a à tester des exceptions. Junit fournit pour cela différents pattern. Nous allons exposer leurs limites à travers un exemple simple et montrer comment on peut faire beaucoup plus simple avec la librairie <strong>Catch-exception</strong>.</p>

<b>Exemple de code à tester</b>

[code language="java"]
public class Calculator {

  public double squareRoot(int x)throws IllegalArgumentException{
    if (x&amp;lt;0){
      throw new IllegalArgumentException(&amp;quot;Could not calculate square root of a negative number&amp;quot;);
    } else {
      return sqrt(x);
    }
  }

  public double divide(int x, int y) throws IllegalArgumentException{
    if (y==0){
      throw new IllegalArgumentException(&amp;quot;Could not divide by 0&amp;quot;);
    } else {
      return x/y;
    }
  }
}
[/code]

<p style="text-align: justify;">Notre calculateur définit deux opérations qui déclarent toutes l'exception <em><strong>IllegalArgumentEception</strong></em>, avec des messages différents. L'idée est d'écrire un test qui détermine avec précision laquelle des exceptions est levée.</p>

<h2>fail() avec try catch</h2>

[code language="java"]
  @Test
  public void exp0_should_throw_exception_when_calculating_square_root_of_negative_number(){
    try {
      calculator.squareRoot(-10);
      fail(&amp;quot;Should throw exception when calculating square root of a negative number&amp;quot;);
    }catch(IllegalArgumentException aExp){
      assert(aExp.getMessage().contains(&amp;quot;negative number&amp;quot;));
    }
  }
[/code]

<p style="text-align: justify;">Le test passe si l'exception spécifiée dans le try-catch est levée, sinon il échoue à l’exécution de la méthode fail() avec le message suivant “<i>Should throw exception when calculating square root of a negative number</i>”. On peut ensuite ajouter des assertions supplémentaires dans le bloc catch. C'est un pattern had-oc qui n'est pas dédié au test, il peut rendre le code de test moins lisible.</p>

<h2>Expected Annotation</h2>

[code language="java"]
  @Test(expected=IllegalArgumentException.class)
  public void exp1_should_throw_exception_when_calculating_square_root_of_negative_number() {
    calculator.divide(100,0);
    calculator.squareRoot(-10);
  }
[/code]

<p style="text-align: justify;">Ici le code est beaucoup plus concis. Le test passe quand l'exception spécifiée dans la propriété expected de l'annotation @Test est levée. On rencontre une des limites de cette approche dans le cas où on a plusieurs instructions susceptibles de lever le même type d'exception. Dans l'exemple ci-dessus on ne sait pas forcément de quelle ligne vient l'exception . Il est aussi impossible de faire des assertions sur les états des objets utilisés dans le test. On ne peut pas non plus faire des tests sur les propriétés de l'exception, comme le message ou le code d'erreur, qui peuvent être utiles pour lever l’ambiguïté dans certaines situations. Ce pattern n'est donc à utiliser que pour des cas très simples.</p>

<h2>JUnit @Rule et ExpectedException</h2>

[code language="java"]
  @Rule
  public ExpectedException thrown = ExpectedException.none();

  @Test
  public void exp2_should_throw_exception_when_calculating_square_root_of_negative_number(){
    thrown.expect(IllegalArgumentException.class);
    thrown.expectMessage(JUnitMatchers.containsString(&amp;quot;negative number&amp;quot;));

    calculator.squareRoot(-10);
  }
[/code]

<p style="text-align: justify;">On commence par déclarer thrown qui peut être utilisé dans toutes les méthodes de la classe de tests. Contrairement à Expected on peut faire une assertion sur le contenu du message, avec des matchers de Junit si on veut avoir plus de précisions. Le message suivant s'affichera si aucune exception n'est levée <i>"Expected test to throw (exception with message a string containing "negative number" and an instance of java.lang.IllegalArgumentException"</i>. Mais là non plus aucune assertion n'est possible sur les états des objets utilisés.</p>

<h2>Catch-exception</h2>

<a href="http://code.google.com/p/catch-exception/">http://code.google.com/p/catch-exception/</a>

[code language="java"]
  @Test
  public void exp3_should_throw_exception_when_calculating_square_root_of_negative_number(){
    catchException(calculator).squareRoot(-10);

    assert caughtException() instanceof IllegalArgumentException;
  }
[/code]

Dépendance Maven

[code language="xml"]

    com.googlecode.catch-exception
    catch-exception
    1.2.0
    test

[/code]

<p style="text-align: justify;">La grande différence qu'elle apporte par rapport aux précédents exemples est qu'elle respecte le très commun pattern <a href="http://www.arrangeactassert.com/why-and-what-is-arrange-act-assert/">Arrange-Act-Assert</a>. Elle est externe à <strong>Junit</strong> et peut être utilisée avec d'autres frameworks de test comme <strong>Test NG.</strong> Le code est concis et facile à lire. On peut savoir à partir de quel appel l'exception a été générée, tester plusieurs exceptions à la fois et ajouter des assertions sur les états des objets et les propriétés des exceptions.</p>

N'hésitez pas à lire <span style="color: #003366;"><strong><a href="https://github.com/Codearte/catch-exception/blob/master/catch-exception/src/main/java/com/googlecode/catchexception/CatchException.java"><span style="color: #003366;">la documention </span></a></strong></span>pour explorer toute sa richesse et sa simplicité, et son utilisation avec les matchers.
