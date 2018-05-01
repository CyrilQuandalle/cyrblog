---
layout: post
title: "Linq provider : un essai… partie 6"
date: 2012-07-23 16:58
author: pierre irrmann
comments: true
categories: [C#, Jam de Code, LINQ, Outils, Programmation, testing]
---
<h3 style="text-align: justify;"><strong><span style="color: #333333;">Jammin’ Jammin’</span></strong></h3>

<p style="text-align: justify;">Le mois dernier, j’ai eu l’opportunité d’obtenir du feedback sur mon provider Linq, de la part <a href="http://twitter.com/#%21/cyriux" target="_blank">@cyriux</a> et Jérôme, au cours de la Jam de code mensuelle d’<a href="https://twitter.com/ArollaFr" target="_blank">@ArollaFr</a>. A cette occasion, nous avons également fait d’importants changements dans le code.</p>

<p style="text-align: justify;">Le concept d’un Jam de code est simple : nous nous rencontrons une fois par mois pour mettre en pratique nos compétences en programmation, en mettant l’accent sur des pratiques telles que TDD et le pair-programming.</p>

<p style="text-align: justify;">Pour plus de détails : http://www.arolla.fr/evenements-2/jams-de-code/</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/07/Montage-jam.jpg"><img class="alignleft size-full wp-image-921" title="Montage jam" src="http://www.arolla.fr/blog/wp-content/uploads/2012/07/Montage-jam.jpg" alt="" width="951" height="174" /></a></p>

<p style="text-align: justify;">Le thème du mois de juin était “Tester des choses qui sont difficiles à tester”. La session a démarré sur un “Lightning talk” par <a href="http://twitter.com/#%21/cyriux" target="_blank">@cyriux</a>, qui nous a décrit un certain nombre de situations dans lesquelles la mise en place de tests est relativement compliquée, comme par exemple :</p>

<ul style="text-align: justify;">
    <li>Lorsque le temps est impliqué</li>
    <li>Lorsqu’on traite de parallélisation et de multi-threading</li>
    <li>Lorsque l’architecture fait partie du test (bande passante du réseau, systèmes tolérants aux pannes…)</li>
    <li>Tester la sécurité (injection SQL, diverses sources d’exploits)</li>
    <li>Lorsque l’hôte de l’application est difficile à émuler (par exemple: comment tester un plug-in eclipse ?)</li>
    <li>Tester des entrées/sorties non triviales : images, sons, documents, rapports, messages de taille importante…</li>
    <li>Tester avec des problèmes de configuration vaste : index de recherche réaliste, algorithmes sociaux, séries temporelles pour des analyses financières…</li>
</ul>

<p style="text-align: justify;">Après cette introduction, nous avons recherché un exemple pratique d’une telle situation, et j’ai suggéré que nous jettions un œil à mon implémentation de ce provider Linq. Je ne suis pas allé très loin dans cette implémentation à ce jour, mais l’objectif de ce provider “Linq to web service” est de récupérer des données filtrées par un service en se basant sur une requête Linq.</p>

<p style="text-align: justify;">Cela signifie que je souhaite que le filtrage des données intervienne au niveau du web service, et non au niveau du client de ce service. Et bien sûr, il s’agit d’un comportement que je veux pouvoir tester ! Le mécanisme du fonctionnement du provider est résumé sur le schéma suivant :</p>

<p style="text-align: justify;"><img style="float: none; margin-left: auto; margin-right: auto;" src="http://www.pirrmann.net/wp-content/uploads/2012/06/linq-provider-schema-006.png" alt="" /></p>

<p style="text-align: justify;">Après une courte discussion, <a href="http://twitter.com/#%21/cyriux" target="_blank">@cyriux</a> m’a fait remarqué qu’il y avait plusieurs fausses assomptions dans la façon dont je testais ces points. J’étais parti du principe que j’allais tester le provider face à un service que j’aurais implémenté moi-même pour l’occasion… mais en réalité je n’avais aucun besoin d’une implémentation réelle du service ! Le service en lui-même, et le provider correspondant, sont deux fonctionnalités liées, mais distinctes, et doivent être testées unitairement indépendamment l’une de l’autre !</p>

<p style="text-align: justify;">Je n’ai pas encore complètement abandonné l’idée d’implémenter ce service, mais il s’agit à présent d’un objectif séparé, qui n’a plus rien à voir avec l’implémentation du provider. Et le fait que le service est en fait un web service est juste un détail d’implémentation. Cette réflexion m'a amené à revoir l'organisation du projet. Ma vision de cette organisation est désormais la suivante :</p>

<p style="text-align: justify;"><img style="float: none; margin-left: auto; margin-right: auto;" src="http://www.pirrmann.net/wp-content/uploads/2012/07/linq-provider-schema-007.png" alt="" /></p>

<p style="text-align: justify;">La solution est dorénavant composée de 3 assemblies :</p>

<ul style="text-align: justify;">
    <li><em>PeopleFinder</em>, qui définit l’interface du service (le contrat) et quelques classes utilisées comme simples Data Transfer Objects,</li>
    <li><em>LinqToService</em>, qui contient l’implémentation du Linq provider, dont traitent les billets précédents,</li>
    <li><em>UnitTests</em>, qui contient les tests unitaires, ainsi qu’une implémentation “lambda-based” de l’interface PeopleFinder. Cette implémentation est utilisée pour les tests à la place du web service.</li>
</ul>

<p style="text-align: justify;">Cette organisation nous permet de tester uniquement le comportement du provider, en fournissant notre propre implémentation du service. Pour permettre l’injection de notre implémentation du service, nous avons introduit une classe PeopleFinderLocator.</p>

<p style="text-align: justify;">Pour une expression Linq donnée, le but du provider Linq est uniquement de fournir au service les bons paramètres, de récupérer les données transmises par le service, et d’exécuter le travail additionnel que le service ne peut pas (ou ne sait pas encore) gérer. Dans le cas présent, le service sait comment filtrer des personnes en fonction de leur âge. Considérons donc la requête suivante :</p>

<pre>var query = new QueryableServiceData&lt;Person&gt;()
    .Where(p =&gt; p.Age &gt;= 36);</pre>

<p style="text-align: justify;">Lorsqu’on l’exécute, le provider doit appeler le service avec le paramètre approprié, et doit renvoyer les résultats à l’appelant, sans effectuer aucun autre travail additionnel. La situation devrait donc être la suivante :</p>

<p style="text-align: justify;"><img style="width: 480px; float: none; margin-left: auto; margin-right: auto;" src="http://www.pirrmann.net/wp-content/uploads/2012/07/linq-provider-schema-009.png" alt="" /></p>

<p style="text-align: justify;">Voici le test que j’ai mis en place, correspondant à cette situation précise :</p>

<pre>[TestMethod]
public void GivenAProviderWhenIFilterOnAgeGT36ThenItFilters()
{
    LambdaBasedPeopleFinder peopleFinder =
        new LambdaBasedPeopleFinder(p =&gt; p.Age &gt;= 36);
    PeopleFinderLocator.Instance = peopleFinder;

    QueryableServiceData&lt;Person&gt; people =
        new QueryableServiceData&lt;Person&gt;();

    var query = people
        .Where(p =&gt; p.Age &gt;= 36);

    var results = query.ToList();

    Assert.IsTrue(results.All(p =&gt; p.Age &gt;= 36));
    Assert.AreEqual(
        new IntCriterion()
            {
                FilterType = NumericFilterType.IsGreaterThan,
                Value = 36
            },
            peopleFinder.PassedCriteria.Age);
}</pre>

<p style="text-align: justify;">Dans le test précédent, on utilise l’implémentation du service LambdaBasedPeopleFinder, qui effectue le filtrage des données auquel on s’attend, basé sur un prédicat. D’une certaine façon, on dépend de cette implémentation, car nous testons que les résultats sont correctement filtrés. Mais nous pouvons aller plus loin, et tester que le provider ne fait pas le travail qu’il doit déléguer au service !</p>

<p style="text-align: justify;">Si nous adaptons légèrement le test précédent en remplaçant le lambda fourni au service par p =&gt; true, les résultats en retour du service ne seront pas filtrés, et nous pouvons vérifier que le provider délègue bien son travail, et ne filtre pas les données.</p>

<pre>[TestMethod]
public void WhenIFilterOnAgeGT36ThenTheProviderDoesntFilter()
{
    LambdaBasedPeopleFinder peopleFinder =
        new LambdaBasedPeopleFinder(p =&gt; true);
    PeopleFinderLocator.Instance = peopleFinder;

    QueryableServiceData&lt;Person&gt; people =
        new QueryableServiceData&lt;Person&gt;();

    var query = people
        .Where(p =&gt; p.Age &gt;= 36);

    var results = query.ToList();

    Assert.IsFalse(results.All(p =&gt; p.Age &gt;= 36));
    Assert.AreEqual(
        new IntCriterion()
        {
            FilterType = NumericFilterType.IsGreaterThan,
            Value = 36
        },
        peopleFinder.PassedCriteria.Age);
}</pre>

<p style="text-align: justify;">Finalement, tester le provider n’était pas un problème compliqué (pour des cas aussi simples). Mon problème n’était pas “comment tester”, comme je le pensais au début, mais plutôt “quoi tester”.</p>

<p style="text-align: justify;">Pour finir, voici la liste des prochaines choses que je souhaite faire sur ce projet (l’ordre peut changer) :</p>

<ul style="text-align: justify;">
    <li>Mettre en œuvre SpecFlow pour transformer tous mes tests et utiliser <a href="https://github.com/cucumber/cucumber/wiki/Gherkin" target="_blank">la syntaxe Gherkin</a>,</li>
    <li>Prendre en charge des requêtes plus complexes, lister tous les types de nœuds qui peuvent être rencontrés et tous les gérer, en s’assurant que les données retournées sont toujours correctes. Si nécessaire, tout nœud non géré pourrait entrainer l’utilisation d’un critère de recherche vide, et la réalisation de toutes les opérations de la requête en utilisant Linq to Objects, au détriment de la performance,</li>
    <li>Implémenter le service, même si ce n’est pas nécessaire, juste pour la satisfaction de le faire,</li>
    <li>Rester coincé sur la manipulation des arbres d’expression sans savoir comment m’y prendre,</li>
    <li>Publier la solution sur un répository Github,</li>
    <li><span style="text-decoration: underline;">Obtenir de l’aide de la part de toute personne motivée</span>.</li>
</ul>

<p style="text-align: justify;"><a href="http://twitter.com/#%21/pirrmann" target="_blank">@pirrmann</a></p>
