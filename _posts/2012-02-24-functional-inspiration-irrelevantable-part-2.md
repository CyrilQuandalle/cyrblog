---
layout: post
title: Inspiration fonctionnelle - Irrelevantable, partie 2
date: 2012-02-24 05:51
author: pierre irrmann
comments: true
categories: [développement, F#, Fonctionnel, fonctionnel, Programmation]
---
<h3>Tentons d’exprimer quelque chose de plus que Nullable&lt;T&gt;</h3>  <p>Pas de soirée Craftsmanship hier soir pour moi… un peu déçu, mais au moins j’en ai profité pour avancer sur la mise en ligne de ce billet !</p>  <p>Il s’agit du troisième billet d’une série sur la façon dont la programmation fonctionnelle, et la <a href="http://www.arolla.fr/blog/2011/12/formation-f-avec-robert-pickering/">formation F# à laquelle j’ai récemment assisté</a>, m’apportent de l’inspiration dans mon travail quotidien en C#. Ce post présentera une implémentation en F# du type Irrelevantable précédemment construit en C#.</p>  <p>Passons directement au code, voici la structure :</p>  <pre><span style="color: blue">type </span><span style="color: black">Irrelevantable&lt;'a&gt; =
    | Relevant </span><span style="color: blue">of </span><span style="color: black">'a
    | Absent
    | Irrelevant</span></pre>

<p>En utilisant un simple “discriminated union type”, on peut construire sans artifice une structure gérant les trois cas nécessaires.</p>

<p>Voyons maintenant comment ce type peut être utilisé. Je commence par définir un type Product et une liste de produits :</p>

<pre><span style="color: blue">type </span><span style="color: black">Product =
  { Name: string;
    ProductType: string;
    Strike: Irrelevantable&lt;decimal&gt;;
    Premium: Irrelevantable&lt;decimal&gt;;
    TradedPrice: Irrelevantable&lt;decimal&gt;
    SwapPrice: Irrelevantable&lt;decimal&gt;; }

</span><span style="color: blue">let </span><span style="color: black">products =
  [ { Name = </span><span style="color: maroon">&quot;568745&quot;</span><span style="color: black">;
      ProductType = </span><span style="color: maroon">&quot;Option&quot;</span><span style="color: black">;
      Strike = Relevant(176m);
      Premium = Relevant(3.72m);
      TradedPrice = Irrelevant;
      SwapPrice = Irrelevant } ;
    { Name = </span><span style="color: maroon">&quot;568746&quot;</span><span style="color: black">;
      ProductType = </span><span style="color: maroon">&quot;Option&quot;</span><span style="color: black">;
      Strike = Relevant(176m);
      Premium = Absent;
      TradedPrice = Irrelevant;
      SwapPrice = Irrelevant } ;
    { Name = </span><span style="color: maroon">&quot;568747&quot;</span><span style="color: black">;
      ProductType = </span><span style="color: maroon">&quot;Swap  &quot;</span><span style="color: black">;
      Strike = Irrelevant;
      Premium = Irrelevant;
      TradedPrice = Relevant(15.3m);
      SwapPrice = Relevant(15.6m) } ]</span></pre>

<p>L’objectif est maintenant d’afficher un rapport basé sur ces produits. Je définis pour cela quelques fonctions utilitaires et/ou de formatage :</p>

<pre><span style="color: blue">let </span><span style="color: black">formatValue formatter value =
    </span><span style="color: blue">match </span><span style="color: black">value </span><span style="color: blue">with
    </span><span style="color: black">| Relevant(data) </span><span style="color: blue">-&gt; </span><span style="color: black">(formatter data)
    | Absent </span><span style="color: blue">-&gt; </span><span style="color: maroon">&quot;&quot;
    </span><span style="color: black">| Irrelevant </span><span style="color: blue">-&gt; </span><span style="color: maroon">&quot;-&quot;

</span><span style="color: blue">let </span><span style="color: black">padLeft (value:string) =
    value.PadLeft(7)

</span><span style="color: blue">let </span><span style="color: black">formatPrice =
    formatValue (</span><span style="color: blue">fun </span><span style="color: black">(d:decimal) </span><span style="color: blue">-&gt; </span><span style="color: black">d.ToString(</span><span style="color: maroon">&quot;N2&quot;</span><span style="color: black">))
    
</span><span style="color: blue">let </span><span style="color: black">getLine vals =
    System.String.Join(</span><span style="color: maroon">&quot; | &quot;</span><span style="color: black">, vals |&gt; Seq.map padLeft)

</span><span style="color: blue">let </span><span style="color: black">getValues p = 
    p.Name ::
    p.ProductType ::
    (formatPrice p.Strike) ::
    (formatPrice p.Premium) ::
    (formatPrice p.TradedPrice) ::
    (formatPrice p.SwapPrice) :: []

</span><span style="color: blue">let </span><span style="color: black">getProductLine =
    getValues &gt;&gt; getLine</span></pre>

<p>On peut remarque ici qu’en jouant avec la syntaxe F#, en plus d’utiliser l’opérateur de pipe ( |&gt; ), j’ai également utilisé une composition de fonctions, grâce à l’opérateur &gt;&gt; (qui n’est pas une redirection de flux C++ !).</p>

<p>Et j’utilise ces fonctions pour générer les lignes à afficher, en commençant par une ligne d’en-tête :</p>

<pre><span style="color: blue">let </span><span style="color: black">headerLine =
  ( </span><span style="color: maroon">&quot;Ref.&quot; </span><span style="color: black">::
    </span><span style="color: maroon">&quot;Type&quot; </span><span style="color: black">::
    </span><span style="color: maroon">&quot;Strike&quot; </span><span style="color: black">::
    </span><span style="color: maroon">&quot;Premium&quot; </span><span style="color: black">::
    </span><span style="color: maroon">&quot;Price&quot; </span><span style="color: black">::
    </span><span style="color: maroon">&quot;Swap price&quot; </span><span style="color: black">:: [])
  |&gt; getLine

</span><span style="color: blue">let </span><span style="color: black">productLines =
  products |&gt; List.map getProductLine

</span><span style="color: blue">let </span><span style="color: black">allLines =
    headerLine :: productLines</span></pre>

<p>Finalement, je peux afficher le rapport :</p>

<pre><span style="color: blue">let </span><span style="color: black">test = allLines |&gt; List.iter (printfn </span><span style="color: maroon">&quot;%s&quot;</span><span style="color: black">)</span></pre>

<p>Et l’affichage en sortie est le suivant :</p>

<pre><span style="color: black">   Ref. |    Type |  Strike | Premium |   Price | Swap price
 568745 |  Option |  176,00 |    3,72 |       - |       -
 568746 |  Option |  176,00 |         |       - |       -
 568747 |  Swap   |       - |       - |   15,30 |   15,60</span></pre>

<p>On peut alors remarquer la différence entre une valeur absente (dans la colonne Premium pour le deuxième produit), et les valeurs qui n’ont aucun sens, et qui sont représentées par un tiret.</p>

<p><a href="http://twitter.com/#%21/pirrmann">@pirrmann</a></p>
