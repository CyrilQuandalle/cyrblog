---
layout: post
title: Inspiration Fonctionnelle - La méthode d’extension “With”
date: 2012-02-22 13:14
author: pierre irrmann
comments: true
categories: [développement, F#, Fonctionnel, fonctionnel, Programmation]
---
<h3>Ajouter une méthode d’extension “With” imitant F#</h3>  <p>Ce billet est le premier d’une série sur la façon dont la programmation fonctionnelle, et la <a href="http://www.arolla.fr/blog/2011/12/formation-f-avec-robert-pickering/">formation F# à laquelle j’ai récemment assisté</a>, m’apportent de l’inspiration dans mon travail quotidien en C#. Ce premier billet montre comment mettre en place une fonctionnalité similaire au “with” de F#.</p><!--more-->Une des nombreuses fonctionnalités F#, qui m’a interpelé durant le cours “Débuter en F#” de Robert Pickering, a été le principe des Record Types et leur facilité d’utilisation , par rapport à un Tuple, dont les propriétés ne peuvent pas être nommées.   <p>Par exemple, cet extrait de code F# définit un Record Type “Person” comportant quelques propriétés.</p>  <pre><span style="color: blue">type </span><span style="color: black">Person =
    { Name: string;
      Town: string;
      Country: string;
      Age: int }</span></pre>

<p>&#160;</p>

<p>Une fois ce type défini, il est possible d’en créer une instance :</p>

<pre><span style="color: blue">let </span><span style="color: black">someone =
    { Name = <span style="color: #a31515">&quot;Luc&quot;</span>;
      Town = <span style="color: #a31515">&quot;Paris&quot;</span>;
      Country = <span style="color: #a31515">&quot;France&quot;</span>;
      Age = 29 }</span></pre>

<p>&#160;</p>

<p>Et une fonctionnalité très pratique du F# permet alors d’écrire :</p>

<pre><span style="color: blue">let </span><span style="color: black">someoneElse =
    { someone <span style="color: blue">with</span>
        Age = 30 }</span></pre>

<p>Le mot-clé “with” permet de définir “someoneElse” comme une copie de “someone”, en précisant uniquement les propriétés à changer.</p>

<p>Dans un projet sur lequel j’ai travaillé récemment, j’ai rencontré exactement ce besoin, de définir une instance semblable à une autre, avec très peu de changements, mais cette fois en C#. Afin de réaliser ceci, j’ai écrit une simple méthode d’extension, basée sur l’interface ICloneable :</p>

<pre><span style="color: blue">public static class </span><span style="color: #2b91af">ClonableExtensions
</span><span style="color: black">{
    </span><span style="color: blue">public static </span><span style="color: black">T With&lt;T&gt;(</span><span style="color: blue">this </span><span style="color: black">T original,
                            </span><span style="color: #2b91af">Action</span><span style="color: black">&lt;T&gt; withAction)
        </span><span style="color: blue">where </span><span style="color: black">T : </span><span style="color: #2b91af">ICloneable
    </span><span style="color: black">{
        T copy = (T)original.Clone();
        withAction(copy);
        </span><span style="color: blue">return </span><span style="color: black">copy;
    }
}</span></pre>

<p>
  <br />Avec cette méthode d’extension, si on définit une classe “Person” implémentant l’interface ICloneable, comme ceci : 

  <br /></p>

<pre><span style="color: blue">public class </span><span style="color: #2b91af">Person </span><span style="color: black">: </span><span style="color: #2b91af">ICloneable
</span><span style="color: black">{
    </span><span style="color: blue">public string </span><span style="color: black">Name { </span><span style="color: blue">get</span><span style="color: black">; </span><span style="color: blue">set</span><span style="color: black">; }
    </span><span style="color: blue">public string </span><span style="color: black">Town { </span><span style="color: blue">get</span><span style="color: black">; </span><span style="color: blue">set</span><span style="color: black">; }
    </span><span style="color: blue">public string </span><span style="color: black">Country { </span><span style="color: blue">get</span><span style="color: black">; </span><span style="color: blue">set</span><span style="color: black">; }
    </span><span style="color: blue">public int </span><span style="color: black">Age { </span><span style="color: blue">get</span><span style="color: black">; </span><span style="color: blue">set</span><span style="color: black">; }

    </span><span style="color: blue">public object </span><span style="color: black">Clone()
    {
        </span><span style="color: blue">return new </span><span style="color: #2b91af">Person</span><span style="color: black">()
        {
            Name = </span><span style="color: blue">this</span><span style="color: black">.Name,
            Town = </span><span style="color: blue">this</span><span style="color: black">.Town,
            Country = </span><span style="color: blue">this</span><span style="color: black">.Country,
            Age = </span><span style="color: blue">this</span><span style="color: black">.Age
        };
    }
}</span></pre>

<p>
  <br />Il est maintenant possible d’utiliser la méthode générique With pour écrire le code suivant : 

  <br /></p>

<pre><span style="color: #2b91af">Person </span><span style="color: black">someone = </span><span style="color: blue">new </span><span style="color: #2b91af">Person</span><span style="color: black">()
{
    Name = </span><span style="color: #a31515">&quot;Luc&quot;</span><span style="color: black">,
    Town = </span><span style="color: #a31515">&quot;Paris&quot;</span><span style="color: black">,
    Country = </span><span style="color: #a31515">&quot;France&quot;</span><span style="color: black">,
    Age = 29
};

</span><span style="color: #2b91af">Person </span><span style="color: black">someoneElse = someone.With(p =&gt; p.Age = 30);</span></pre>

<p>Je trouve cette construction à la fois expressive et lisible, mais il faut tout de même noter quelques inconvénients :</p>

<ol>
  <li>L’objet C# doit être muable.&#160; En effet, sa propriété “Age” est mise à jour après la création de l’objet par la méthode Clone. Même si la méthode Clone aurait pu utiliser un constructeur de copie plutôt que d’écrire dans les propriétés publiques, la méthode générique With doit pouvoir par la suite écraser les valeurs de ces propriétés (une solution à ce problème sera proposée dans un prochain billet). </li>

  <li>La syntaxe à utiliser lorsqu’on souhaite modifier plus d’une propriété est moins lisible, car une expression lambda à plusieurs instructions doit être placée entre accolades. </li>
</ol>

<p style="text-align: justify"><a href="http://twitter.com/#%21/pirrmann">@pirrmann</a></p>
