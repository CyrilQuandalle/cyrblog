---
layout: post
title: "Linq provider : un essai… partie 2"
date: 2012-06-19 11:40
author: pierre irrmann
comments: true
categories: [AST, C#, LINQ, Outils, Programmation]
---
<h3 style="text-align: justify;"><span style="color: #333333;">Vous attendez depuis longtemps la suite de l'article précédent </span><span style="color: #333333;"><a href="http://www.arolla.fr/blog/2012/04/linq-provider-un-essai-partie-1/" target="_blank">Linq provider : un essai… partie 1</a></span><span style="color: #333333;">,</span><span style="color: #333333;"> alors la voici !</span></h3>

<p style="text-align: justify;">Tout d'abord, préparons le terrain:</p>

<p style="text-align: justify;">Pour construire mon exemple de provider, je vais interroger une Web service. Tout d’abord, voyons de manière générale ce que je cherche à faire :</p>

<ul>
    <li style="text-align: justify;"><em>DummyWs</em> : mon exemple de provider va être construit sur la base d’un web service. Ce service va renvoyer des objets de son modèle, correspondant à des critères de recherche (des objets <em>Person</em> dans un premier temps, et peut-être d’autres types par la suite).</li>
    <li style="text-align: justify;"><em>DummyWsModel</em> : les classes du modèle et les entités vont être regroupées dans un assembly indépendant du service, et qui sera partagé par tous les projets de la solution.</li>
    <li style="text-align: justify;"><em>DummyWsLinqer</em> : le provider sera dépendant à la fois du modèle et du service.</li>
    <li style="text-align: justify;"><em>Tests</em>: ce projet va utiliser le provider Linq pour consommer des données du service, mais ne contiendra aucune référence directe au service. L’idée ici est de vérifier :
<ul>
    <li style="text-align: justify;">que les données récupérées sont correctes,</li>
    <li style="text-align: justify;">que les parties de la requête qui peuvent être gérées par le service ont bien effectivement été gérées par le service et non par le client.</li>
</ul>
</li>
</ul>

<p style="text-align: justify;">Voici un schéma représentant les dépendances entre les projets :</p>

<img class="aligncenter" src="http://www.pirrmann.net/wp-content/uploads/2012/04/linq-provider-schema-001.png" alt="" />

<p style="text-align: justify;">Je vais commencer avec un modèle très simple, formé d’une seule entité nommée Person. J’enrichirai probablement le modèle lors des étapes suivantes, lorsque j’aurai déjà quelque chose d’opérationnel. Pour le moment, nous avons donc une personne, avec un ID, qui est un GUID, un prénom, un nom de famille et un âge.</p>

<img src="http://www.pirrmann.net/wp-content/uploads/2012/04/linq-provider-schema-002.png" alt="" />

<p style="text-align: justify;">Mon premier objectif va être de faire en sorte que le provider appelle effectivement le service web, et qu’il retourne les résultats. Pour cela, je vais avoir à capturer l’arbre d’expression représentant l’appel, et remplacer dans cet arbre l’expression qui représente les personnes par les résultats du web service.</p>

<p style="text-align: justify;">J’ai basé mon travail sur les Exemples fournis avec Visual Studio (2008 à l’origine), et en particulier le tutoriel <a href="http://msdn.microsoft.com/en-us/library/bb546158.aspx" target="_blank">"Creating an IQueryable LINQ Provider"</a>. La majorité du code basique est issue de cet exemple. Mais bien que l’exemple en question ne soit pas particulièrement compliqué au final, il mélange plusieurs sujets et objectifs, et je l’ai trouvé assez difficile à comprendre. C’est pour cette raison que j’ai voulu réaliser ma propre implémentation, en avançant à tout petits pas.</p>

<p style="text-align: justify;">Étant donné que je sais que mon service va renvoyer des données (donc au moins une personne), le test le plus simple auquel j’ai pu penser pour mon provider est d’obtenir une instance d’un énumérateur et de vérifier que l’on puisse itérer au moins une fois.</p>

<pre>public void GivenAProviderWhenIGetAnEnumeratorThenICanMoveNext()
{
    QueryableDummyData&lt;Person&gt; queryablePersons =
        new QueryableDummyData&lt;Person&gt;();

    bool exists = false;
    using (IEnumerator&lt;Person&gt; enumerator =
        queryablePersons.GetEnumerator())
    {
        exists = enumerator.MoveNext();
    }

    Assert.IsTrue(exists);
}</pre>

<p style="text-align: justify;">Le code précédent fait partie du projet “Tests”, et à partir du premier schéma, nous savons que seuls les projets <em>DummyWsModel</em> et <em>DummyWsLinqer</em> sont référencés. <em>Person</em> est une classe du modèle, et <em>QueryableDummyData&lt;T&gt;</em> est le point d’entrée pour écrire des requêtes basées sur le modèle. Il est à noter qu’il n’y a ici aucune référence directe au web service.</p>

<p style="text-align: justify;">De l’exemple MSDN, je n’ai extrait que les composants essentiels :</p>

<ul style="text-align: justify;">
    <li>une classe à utiliser comme point d’entrée : <em>QueryableDummyData</em>&lt;T&gt;, une implémentation de IOrderedQueryable&lt;T&gt;</li>
    <li>le provider : <em>DummyQueryProvider</em>, une implémentation de IQueryProvider,</li>
    <li><em>DummyQueryContext</em>, la classe qui gère réellement les requêtes, et les transforme de manière à ce qu’elles puissent être exécutées.</li>
    <li><em>TypeSystem</em>, un classe utilitaire, interne à l’assembly.</li>
</ul>

<p style="text-align: justify;">Les classes <em>QueryableDummyData</em>,<em> DummyQueryProvider</em> et <em>TypeSystem</em> sont grosso-modo des copier-coller (Honte sur moi !) depuis l’exemple MSDN, et très peu modifiées excepté le fait qu'elles ont été renommées. <em>DummyQueryContext</em> est l’endroit où le travail est réellement effectué.</p>

<p style="text-align: justify;"><em>QueryableDummyData</em> implémente IOrderedQueryable&lt;T&gt;, ce qui signifie également IQueryable&lt;T&gt;, IEnumerable&lt;T&gt;, IOrderedQueryable, IQueryable et IEnumerable. Parmi toutes ces interfaces, les implémentations réellement nécessaires sont 3 propriétés de IQueryable, les surcharges génériques et non-génériques de GetEnumerator, provenant respectivement des interfaces IEnumerable&lt;T&gt; et IEnumerable. Cette classe n’effectue aucun travail vraiment significatif, et les implémentations de GetEnumerator délèguent tout le travail à la méthode Execute du provider.</p>

<p style="text-align: justify;"><em>DummyQueryProvider</em> implémente IQueryProvider, mais pour le moment les seules méthodes qui vont nous intéresser sont les deux surcharges de Execute (générique and non-générique). A nouveau, ces méthodes ne font rien de plus que déléguer le travail à la classe <em>DummyQueryContext</em>.</p>

<p style="text-align: justify;">Comme je l’ai déjà dit précédemment, <em>DummyQueryContext</em> est l’endroit où il va enfin se passer quelque chose. Pour savoir comment, continuez tout simplement votre lecture avec <a href="http://www.arolla.fr/blog/2012/06/linq-provider-un-essai%E2%80%A6-partie-3/" target="_blank">le post suivant</a> !</p>
