---
layout: post
title: Présentation de NFluent
date: 2014-07-29 10:26
author: didier cauvin
comments: true
categories: [nfluent, Outils, Programmation]
---
<h1 class="entry-title" style="text-align: justify;">1 - Un peu de sémantique</h1>

<p style="text-align: justify;"><a href="http://www.n-fluent.net/">NFluent</a> est une bibliothèque d'assertions dont le but est de proposer l'écriture de tests unitaires de manière "Fluent". D'habitude, je n'aime pas trop mélanger les termes anglais et français. Si on devait traduire littéralement le terme "fluent", cela donnerait "fluide". Qui n'a jamais rêvé d'écrire du code comme on écrirait de manière naturelle :</p>

<p style="text-align: justify;"><code>VérifieQue(MaVariable).EstEgaleA(3);</code></p>

<p style="text-align: justify;">Et bien NFluent nous le permet!</p>

<p style="text-align: justify;">NFluent nous propose donc d'écrire des tests unitaires de manière beaucoup plus fluide qu'avec les outils de tests traditionnels tels que MSTest ou NUnit pour ne citer qu'eux. En fait, il s'utilise "par dessus", à savoir qu'on peut très bien le faire cohabiter avec NUnit par exemple et à moindre frais. Ben oui, avec nuget, rien de plus simple! ;)</p>

<h1 class="entry-title" style="text-align: justify;">2 - Premiers exemples</h1>

<p style="text-align: justify;">Considérons un test sur une chaîne de caractères quelconque. Avec MSTest, on ferait comme suit :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleStringWithMsTestToolTest()<br /> {<br /> var aString = "Hello MSTest";<br /> Assert.AreEqual("Hello MSTest", aString);<br /> }</code></p>

<p style="text-align: justify;">Le même test avec NFluent :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleStringWithNfluentTest()<br /> {<br /> var aString = "Hello NFluent";<br /> Check.That(aString).IsEqualTo("Hello NFluent");<br /> }</code></p>

<p style="text-align: justify;">Avouez que c'est quand même sympa comme style d'écriture. Vous n'êtes pas convaincu? Très bien, prenons un autre exemple :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleListWithMsTestToolTest()<br /> {<br /> var aList = new List&lt;string&gt; {"one", "two", "three"};<br /> Assert.AreEqual(3, aList.Count);<br /> }</code></p>

<p style="text-align: justify;">Avec NFluent :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleListWithNfluentTest()<br /> {<br /> var aList = new List&lt;string&gt; { "one", "two", "three" };<br /> Check.That(aList).HasSize(3);<br /> }</code></p>

<p style="text-align: justify;">On a ici une écriture élégante et surtout nettement plus lisible que celle du dessus! Même un non initié pourrait comprendre!</p>

<p style="text-align: justify;">On peut aussi lier des assertions entre elles. On peut ainsi faire :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleListWithNfluentTest()<br /> {<br /> var aList = new List&lt;string&gt; { "one", "two", "three" };<br /> Check.That(aList).HasSize(3).And.Contains("one");<br /> }</code></p>

<p style="text-align: justify;">Évidemment, on peut très bien utiliser des types plus complexes. Soit une classe <code>MyObject</code> définie comme suit :</p>

<p style="text-align: justify;"><code>class MyObject<br /> {<br /> public string PropertyOne { get; set; }<br /> public string PropertyTwo { get; set; }<br /> }</code></p>

<p style="text-align: justify;">Si on désire vérifier que deux instances de cette classe ne sont pas égales en termes de référence, on ferait comme ceci :</p>

<p style="text-align: justify;">MSTest :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void MyObjectWithMsTestToolTest()<br /> {<br /> var mObject1 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> var mObject2 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> Assert.AreNotSame(mObject1, mObject2);<br /> }</code></p>

<p style="text-align: justify;">NFluent :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void MyObjectWithNfluentTest()<br /> {<br /> var mObject1 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> var mObject2 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> Check.That(mObject1).IsNotEqualTo(mObject2);<br /> }</code></p>

<p style="text-align: justify;">Maintenant, on souhaite vérifier que ces deux instances de <code>MyObject</code> possèdent les mêmes valeurs. Ça se complique un peu pour MSTest :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void MyObjectsAreEqualsWithMsTestToolTest()<br /> {<br /> var mObject1 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> var mObject2 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> Assert.AreEqual(mObject1.PropertyOne, mObject2.PropertyOne);<br /> Assert.AreEqual(mObject1.PropertyTwo, mObject2.PropertyTwo);<br /> }</code></p>

<p style="text-align: justify;">Alors que pour NFluent, c'est ultra simple :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void MyObjectsAreEqualsWithNfluentTest()<br /> {<br /> var mObject1 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> var mObject2 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> Check.That(mObject1).HasFieldsWithSameValues(mObject2);<br /> }</code></p>

<p style="text-align: justify;">Là où NFluent est très fort, c'est qu'il détermine à votre place le type de votre SUT ("System Under Test", ou "unité de test"). Vous avez, je suppose, bien remarqué que je n'ai rien eu à faire pour dire que je manipulais des strings ou des lists.</p>

<p style="text-align: justify;">On peut même tester les exceptions. L'exemple ci-dessous n'est pas très utile en soit mais il illustre mon propos :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> [ExpectedException(typeof(ArgumentException))]<br /> public void SimpleExceptionWithMsTestToolTest()<br /> {<br /> Action action = () =&gt; { throw new ArgumentException(); };<br /> action();<br /> }</code></p>

<p style="text-align: justify;">Ici, on teste si la méthode renvoie une exception de type <code>ArgumentException</code>. Pour cela, on est obligé d'introduire l'attribut <code>ExpectedException</code> sur la méthode de test. Au final, quand on lit, on ne sait pas très bien où le test veut nous emmener.</p>

<p style="text-align: justify;">Avec NFluent, c'est on ne peut plus clair :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleExceptionWithNfluentTest()<br /> {<br /> Action action = () =&gt; { throw new ArgumentException(); };<br /> Check.ThatCode(action).Throws&lt;ArgumentException&gt;();<br /> }</code></p>

<p style="text-align: justify;">De plus, on reste dans la logique d'une assertion par test, et non pas un attribut qui peut facilement nous échapper à la lecture.</p>

<h1 class="entry-title" style="text-align: justify;">3 - Les messages d'erreurs</h1>

<p style="text-align: justify;">La force de NFluent réside aussi dans les messages d'erreurs qu'il renvoie. Reprenons le code du dessus sur les listes pour faire échouer le test :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void SimpleListWithMsTestToolTest()<br /> {<br /> var aList = new List&lt;string&gt; {"one", "two", "three"};<br /> Assert.AreEqual(32, aList.Count);<br /> }</code></p>

<p style="text-align: justify;">Voici ce que nous renvoie MSTest :<br /> <em><br /> Microsoft.VisualStudio.TestTools.UnitTesting.AssertFailedException: Échec de Assert.AreEqual. Attendu : &lt;32&gt;, Réel : &lt;3&gt;.<br /> </em><br /> Avec NFluent :<br /> <em><br /> NFluent.FluentCheckException:<br /> The checked enumerable has 3 elements instead of 32.<br /> The checked enumerable:<br /> ["one", "two", "three"]<br /> </em><br /> Même dans les messages d'erreurs NFluent est fluide! ;)</p>

<p style="text-align: justify;">Autre exemple :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void AnotherSimpleListWithMsTestToolTest()<br /> {<br /> var aList = new List { "one", "two", "three" };<br /> CollectionAssert.AreEquivalent(new List { "one", "two" }, aList);<br /> }</code></p>

<p style="text-align: justify;">Le message d'erreur est le suivant :<br /> <em><br /> Microsoft.VisualStudio.TestTools.UnitTesting.AssertFailedException: Échec de CollectionAssert.AreEquivalent. Le nombre d'éléments dans les collections ne correspond pas. Attendu : &lt;2&gt;, Réel : &lt;3&gt;.<br /> </em><br /> Avec NFluent, on a le détail en plus :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void AnotherSimpleListWithNfluentTest()<br /> {<br /> var aList = new List { "one", "two", "three" };<br /> Check.That(aList).HasSize(3).And.ContainsExactly("one", "two" );<br /> }</code><br /> <em><br /> NFluent.FluentCheckException:<br /> The checked enumerable does not contain exactly the expected value(s).<br /> The checked enumerable:<br /> ["one", "two", "three"] (3 items)<br /> The expected value(s):<br /> ["one", "two"] (2 items)<br /> </em><br /> Enfin, si on reprend la classe MyObject, et qu'on considère le test suivant :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void MyObjectsAreEqualsWithNfluentTest2()<br /> {<br /> var mObject1 = new MyObject() { PropertyOne = "One", PropertyTwo = "Two" };<br /> var mObject2 = new MyObject() { PropertyOne = "Three", PropertyTwo = "Two" };<br /> Check.That(mObject1).HasFieldsWithSameValues(mObject2);<br /> }</code></p>

<p style="text-align: justify;">Le message d'erreur est assez explicite :<br /> <em><br /> NFluent.FluentCheckException:<br /> The checked value's autoproperty 'PropertyOne' (field '&lt;PropertyOne&gt;k__BackingField') does not have the expected value.<br /> The checked value:<br /> ["One"]<br /> The expected value:<br /> ["Three"]<br /> </em></p>

<h1 class="entry-title" style="text-align: justify;">4 - Les extensions</h1>

<p style="text-align: justify;">NFluent propose aussi une mécanique pour écrire ses propres extensions. Prenons par exemple une classe <code>Position</code> qui contient les coordonnées d'un point en X et Y :</p>

<p style="text-align: justify;"><code>public class Position<br /> {<br /> public int X { get; set;}<br /> public int Y { get; set; }<br /> public Position(int x, int y)<br /> {<br /> X = x;<br /> Y = y;<br /> }<br /> }</code></p>

<p style="text-align: justify;">Si on désire tester qu'un point se trouve à une position spécifique, on ferait un test comme celui ci-dessous :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void PositionWithNfluentTest()<br /> {<br /> var myPosition = new Position(1, 1);<br /> Check.That(position).IsNotNull();<br /> Check.That(position.X).IsEqualTo(x);<br /> Check.That(position.Y).IsEqualTo(y);<br /> }</code></p>

<p style="text-align: justify;">Certes le test IsNotNull sur myPosition ne sert pas à grand chose ici, mais si on suppose que l'on obtient myPosition via une autre source, le test prend tout son sens. On constate par ailleurs qu'on a besoin de plusieurs assertions. L'idéal serait de n'en avoir plus qu'une seule. Avec les extensions, c'est possible. Si on considère l'extension suivante sur ICheck&lt;Position&gt;:</p>

<p style="text-align: justify;"><code>public static class PositionExtensions<br /> {<br /> public static void IsAtPosition(this ICheck positionCheck, int x, int y)<br /> {<br /> var checker = ExtensibilityHelper.ExtractChecker(positionCheck);<br /> var position = checker.Value;<br /> Check.That(position).IsNotNull();<br /> Check.That(position.X).IsEqualTo(x);<br /> Check.That(position.Y).IsEqualTo(y);<br /> }<br /> }</code></p>

<p style="text-align: justify;">La classe statique <code>ExtensibilityHelper</code> nous permet d'extraire l'unité de test et de pouvoir ensuite effectuer les tests désirés. Voici ce que devient le test ci-dessus :</p>

<p style="text-align: justify;"><code>[TestMethod]<br /> public void PositionWithNfluentTest()<br /> {<br /> var myPosition = new Position(1, 1);<br /> Check.That(myPosition).IsAtPosition(1, 1);<br /> }</code></p>

<p style="text-align: justify;">Le test est plus lisible et factorisé sans le moindre mal.</p>

<h1 class="entry-title" style="text-align: justify;">5 - Check.That(article).HasComeToAnEnd()</h1>

<p style="text-align: justify;">Voilà, j'espère que cette petite introduction à NFluent vous aura donné envie de vous y mettre. Cette bibliothèque est vraiment facile à mettre en place et est extrêmement plaisante à utiliser (c'est un plaisir d'écriture, vraiment!). Je pense qu'elle peut être un réel atout pour vos projets. Mais si, malgré cela, vous n'êtes toujours pas convaincus, vous pouvez aussi vous rendre <a href="https://github.com/tpierrain/NFluent/blob/master/ReadMe.md#usage-sample">ici</a>! Happy testing! :)</p>
