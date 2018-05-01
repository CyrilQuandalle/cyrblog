---
layout: post
title: Inspiration fonctionnelle - Revisitons la méthode With
date: 2012-02-24 06:10
author: pierre irrmann
comments: true
categories: [développement, F#, Fonctionnel, fonctionnel, immutabilité, Programmation]
---
<h3>Retour sur le tout premier billet pour amélioration</h3>  <p>Ce billet est le quatrième d’une série sur la façon dont la programmation fonctionnelle, et la <a href="http://www.arolla.fr/blog/2011/12/formation-f-avec-robert-pickering/">formation F# à laquelle j’ai récemment assisté</a>, m’apportent de l’inspiration dans mon travail quotidien en C#. Je vais y proposer une autre implémentation de la méthode “With”, qui fonctionne cette fois ci également pour des structure immuables.</p>  <p>Pas de grande explication aujourd’hui, voici tout d’abord une classe immuable représentant la même Person que dans le premier billet :</p>  <pre><span style="color: blue">public class </span><span style="color: #2b91af">Person
</span><span style="color: black">{
    </span><span style="color: blue">public readonly string </span><span style="color: black">Name;
    </span><span style="color: blue">public readonly string </span><span style="color: black">Town;
    </span><span style="color: blue">public readonly string </span><span style="color: black">Country;
    </span><span style="color: blue">public readonly int </span><span style="color: black">Age;

    </span><span style="color: blue">public </span><span style="color: black">Person(</span><span style="color: blue">string </span><span style="color: black">name, </span><span style="color: blue">string </span><span style="color: black">town,
                    </span><span style="color: blue">string </span><span style="color: black">country, </span><span style="color: blue">int </span><span style="color: black">age)
    {
        </span><span style="color: blue">this</span><span style="color: black">.Name = name;
        </span><span style="color: blue">this</span><span style="color: black">.Town = town;
        </span><span style="color: blue">this</span><span style="color: black">.Country = country;
        </span><span style="color: blue">this</span><span style="color: black">.Age = age;
    }
}</span></pre>

<p>On peut remarquer que tous les champs sont définis “readonly”, ce qui rend la structure immuable. Etant donné que les valeurs des champs ne peuvent être mises à jour que dans le constructeur, nous ne serons pas en mesure d’utiliser un délégué en paramètre comme c’était le cas précédemment. Nous allons donc avoir à modifier ces valeurs <strong>à l’intérieur du constructeur</strong>.</p>

<p>Pour ce faire, nous définissions un second constructeur (privé). Surprise ! On utilise la construction Irrelevantable des deux derniers billets :</p>

<pre><span style="color: blue">private </span><span style="color: black">Person(
    </span><span style="color: #2b91af">Person </span><span style="color: black">source,
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; name,
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; town,
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; country,
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">int</span><span style="color: black">&gt; age)
{
    </span><span style="color: blue">this</span><span style="color: black">.Name = name.IsRelevant ? name.Value : source.Name;
    </span><span style="color: blue">this</span><span style="color: black">.Town = town.IsRelevant ? town.Value : source.Town;
    </span><span style="color: blue">this</span><span style="color: black">.Country = country.IsRelevant ? country.Value
                                        : source.Country;
    </span><span style="color: blue">this</span><span style="color: black">.Age = age.IsRelevant ? age.Value : source.Age;
}</span></pre>

<p>Analysons un peu le fonctionnement de ce constructeur. Quand il est appelé, il remplit les différentes propriétés de la classe en utilisant soit la valeur de la propriété correspondante de l’objet Person passé en paramètre, soit un des arguments suivants, en fonction de s’ils sont “appropriés” ou “sans objet”.</p>

<p>Si le constructeur est appelé sans aucune valeur “appropriée”, il agit donc comme un constructeur de copie. S’il est appelé avec une seule valeur “appropriée”, il se comporte exactement comme la méthode d’extension “With” présentée au départ.</p>

<p>Et grâce à l’utilisation des valeurs par défaut, introduites en C# 4.0, on peut rendre les choses à la fois plus pratiques et élégantes :</p>

<pre><span style="color: blue">public </span><span style="color: #2b91af">Person </span><span style="color: black">With(
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; name
        = </span><span style="color: blue">default</span><span style="color: black">(</span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt;),
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; town
        = </span><span style="color: blue">default</span><span style="color: black">(</span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt;),
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt; country
        = </span><span style="color: blue">default</span><span style="color: black">(</span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">string</span><span style="color: black">&gt;),
    </span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">int</span><span style="color: black">&gt; age
        = </span><span style="color: blue">default</span><span style="color: black">(</span><span style="color: #2b91af">Irrelevantable</span><span style="color: black">&lt;</span><span style="color: blue">int</span><span style="color: black">&gt;))
{
    </span><span style="color: blue">return new </span><span style="color: #2b91af">Person</span><span style="color: black">(
        </span><span style="color: blue">this</span><span style="color: black">,
        name: name,
        town: town,
        country: country,
        age: age);
}</span></pre>

<p>La méthode publique “With” définit des valeurs par défaut pour toutes les propriétés, nous permettant de ne fournir en paramètre que les valeurs à modifier.</p>

<pre><span style="color: #2b91af">Person </span><span style="color: black">someone = </span><span style="color: blue">new </span><span style="color: #2b91af">Person</span><span style="color: black">(
    name: </span><span style="color: #a31515">&quot;Luc&quot;</span><span style="color: black">,
    town: </span><span style="color: #a31515">&quot;Paris&quot;</span><span style="color: black">,
    country: </span><span style="color: #a31515">&quot;France&quot;</span><span style="color: black">,
    age: 29);

</span><span style="color: #2b91af">Person </span><span style="color: black">someoneElse = someone.With(age: 30);
</span><span style="color: #2b91af">Person </span><span style="color: black">someAmerican = someone.With(
                            name: </span><span style="color: #a31515">&quot;Luke&quot;</span><span style="color: black">,
                            country: </span><span style="color: #a31515">&quot;United States&quot;</span><span style="color: black">);</span></pre>

<p>Vous pouvez remarquer ici que la syntaxe nous permet même de définir plusieurs changements de manière très concise ! Et cette fois la méthode fonctionne aussi bien pour des structures muables qu’immuables !</p>

<p><a href="http://twitter.com/#%21/pirrmann">@pirrmann</a></p>
