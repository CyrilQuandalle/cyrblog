---
layout: post
title: BDD et SpecFlow pour des tests plus lisibles
date: 2012-09-11 11:27
author: pierre irrmann
comments: true
categories: [AST, bdd, Bonnes pratiques de dév, C#, LINQ, Programmation, SpecFlow]
---
<p style="text-align: justify;">Dans ce billet, je vais utiliser SpecFlow et <a href="https://github.com/cucumber/cucumber/wiki/Gherkin" target="_blank">la syntaxe Gherkin</a> pour écrire des tests en langage naturel. Je souhaite montrer à quel point la mise en place en est facile, lorsque vous disposez de code déjà testable, et comment celle-ci vous aide à écrire de meilleurs tests en quelques minutes.</p>
<p style="text-align: justify;"><img style="margin: 0px 10px 0px 0px; float: left;" src="http://www.pirrmann.net/wp-content/uploads/2012/08/specflow-logo.png" alt="" align="left" />SpecFlow est à la fois un composant ou framework de test, et un plug-in pour Visual Studio, qui permet d’écrire des tests d’une manière naturelle, puis de les exécuter avec votre dérouleur de tests (test-runner) favori. Comme on peut le lire sur leur site (en anglais, traduit ici par mes soins) :</p>

<blockquote>
<p style="text-align: justify;">SpecFlow a pour but de combler le fossé de communication entre les experts du domaine et les développeurs, en liant des spécifications de comportements, lisibles par le métier, avec leur implémentation sous-jacente. Notre mission est de fournir une approche pragmatique et sans friction au développement guidé par les tests d’acceptance (ATDD) et au développement guidé par le comportement (BDD) pour les projets .NET d’aujourd’hui.</p>
</blockquote>
<p style="text-align: justify;">SpecFlow est construit sur la base de <a href="https://github.com/cucumber/cucumber/wiki/Gherkin" target="_blank">la syntaxe Gherkin</a>, une grammaire conçue pour écrire des spécifications de comportement. Cette syntaxe a d’abord émergé de la communauté Ruby, et est liée à un projet nommé Cucumber. Pour plus de détails, vous pouvez consulter le site <a href="http://cukes.info">http://cukes.info</a> :</p>

<blockquote>
<p style="text-align: justify;">Cucumber  est la réécriture par Aslak Hellesøy du “story-runner” de RSpec, qui a été originellement écrit par Dan North. (qui à nouveau était une réécriture de sa première implémentation - RBehave. RBehave était son portage en Ruby de JBehave)…</p>
</blockquote>
<p style="text-align: justify;"><img style="margin: 0px 0px 10px 10px; float: right;" src="http://www.pirrmann.net/wp-content/uploads/2012/08/cuke-logo.png" alt="" width="370" height="71" align="right" />Gherkin est le DSL (<a href="http://en.wikipedia.org/wiki/Domain-specific_language" target="_blank">Domain Specific Language</a>) de Cucumber, et en tant que tel, il n’est pas lié à un langage ou à une technologie particulière. Pour cette raison il peut être utilisé en .NET en utilisant SpecFlow. Cette syntaxe n’a que très peu de mots-clés, mais se révèle très puissante. La meilleure manière d’expliquer son fonctionnement est de montrer un exemple de scénario de test :</p>

<pre>Scenario: Filter on age
    Given I have written a query against the provider
    And I have added a Age &gt;= 36 where clause
    Given The people finder service filters on Age &gt;= 36
    When I execute the query
    Then The service parameter should be Age IsGreaterThan 36
    And The result count should be 3
    And The result should validate the servicePredicate</pre>
<p style="text-align: justify;">Arrivé à ce point du billet, vous aurez peut-être remarqué que je travaille à nouveau sur mon provider Linq… mais dans ce billet je ne parlerai que de test et de l’utilisation de SpecFlow, alors ne fuyez pas, même si vous n’arrivez pas à digérer les arbres d’expressions !</p>
<p style="text-align: justify;">Ce que SpecFlow vous permet de faire, c’est d’écrire vos spécifications et cas de tests sous forme de texte brut, et de générer des classes de tests sur cette base. SpecFlow propose un certain nombre de fonctionnalités particulièrement intéressantes, qui permettent d’organiser les spécifications des tests et des exemples.</p>
<p style="text-align: justify;"><img style="float: right;" src="http://www.pirrmann.net/wp-content/uploads/2012/08/nuget-logo.png" alt="" align="right" /><img style="margin: 0px 10px 0px 0px; float: left;" src="http://www.pirrmann.net/wp-content/uploads/2012/08/vsix-logo.png" alt="" width="64" height="64" align="left" />Afin d’utiliser SpecFlow dans votre projet, la manière la plus simple de procéder est d’ajouter une référence dans votre projet de test, en utilisant le gestionnaire de packages NuGet. Pour obtenir l’intégration à Visual Studio, vous devez installer également l’extension “SpecFlow integration for Visual Studio extension”.</p>
<p style="text-align: justify;">Lorsqu’on développe en BDD, les tests sont écrits pour tester des fonctionnalités et des exemples précis de comportements. Pour chaque fonctionnalité de votre application (ou composant, ou classe), vous pouvez créer un fichier “feature”. Comme vous pouvez le voir dans l’exemple précédent, les tests vont être organisés autour du concept de “scénario”. Chaque fichier “feature” peut contenir plusieurs scénarios (<em>ou scenarii pour les purs latinistes</em>). Le lien entre les scénarios et l’implémentation concrète des tests va s’effectuer ligne à ligne : chaque ligne de scénario va correspondre au final à un appel de méthode.</p>

<ul style="text-align: justify;">
	<li>le clauses “Given” et leurs “And” associés vont correspondre à des méthodes qui mettent en place le contexte des tests à effectuer,</li>
	<li>la clause “When” va déclencher une action ou un évènement qui doit engendrer le comportement que l’on souhaite tester,</li>
	<li>les clauses “Then” et ses “And” associés vont effectuer des assertions qui déterminent le résultat du test.</li>
</ul>
<p style="text-align: justify;">Pour faire correspondre les lignes d’un scénario et les méthodes de la classe de test, ces méthodes sont décorées d’attributs spécifiques à SpecFlow, qui prennent en argument le modèle de la ligne associée. Ce modèle de phrase peut inclure des groupes d’expressions permettant de capturer des valeurs, qui seront utilisées pour renseigner à l’exécution les paramètres de la méthode à appeler.</p>

<pre>[Given(@"I have added a (.*) where clause")]
public void GivenIHaveAddedAWhereClause(string predicate)</pre>
<p style="text-align: justify;">Si nous nous penchons à nouveau sur l’exemple présenté précédemment, la meilleure façon de comprendre comment ce scénario est décliné en appel de méthodes est d’observer la trace générée en sortie de test :</p>

<pre>Given I have written a query against the provider
-&gt; done: FeaturesTest.GivenIHaveWrittenAQuery() (0,0s)
And I have added a Age &gt;= 36 where clause
-&gt; done: FeaturesTest.GivenIHaveAddedAWhereClause("Age &gt;= 36") (0,0s)
Given The people finder service filters on Age &gt;= 36
-&gt; done: FeaturesTest.GivenThePeopleFinderServiceFiltersOn("Age &gt;= 36") (0,0s)
When I execute the query
-&gt; done: FeaturesTest.WhenIExecuteTheQuery() (0,1s)
Then The service parameter should be Age IsGreaterThan 36
-&gt; done: FeaturesTest.ThenTheServiceParameter("Age IsGreaterThan 36") (0,0s)
And The result count should be 3
-&gt; done: FeaturesTest.ThenTheResultCount(3) (0,0s)
And The result should validate the servicePredicate
-&gt; done: FeaturesTest.ThenTheResultShouldValidatetheServicePredicate() (0,0s)</pre>
<p style="text-align: justify;">Nous pouvons clairement voir que chaque ligne du scénario a été utilisée pour appeler une méthode. Pour “accumuler” les conditions et pouvoir effectuer des assertions sur les résultats, nous utilisons une classe qui définit le contexte du test :</p>

<pre>public class ServiceContext
{
    // Data context
    public IEnumerable&lt;Person&gt; AllPeople { get; set; }

    // Input
    public List&lt;string&gt; Query { get; set; }
    public List&lt;string&gt; ServiceLambdas { get; set; }

    // Service predicate, built upon the ServiceLambdas
    public Expression&lt;Func&lt;Person, bool&gt;&gt;
                            ServicePredicate {get; set;}

    // Output
    public List&lt;Person&gt; Results { get; set; }
    public SearchCriteria PassedCriteria { get; set; }
}</pre>
<p style="text-align: justify;">En utilisant cette classe de contexte, les méthodes correspondant aux clauses “Given” renseignent les propriétés utilisées en entrée du test, le “When” exécute l’action à tester (ici, l’évaluation de la requête Linq par le provider), et les “Then” vérifient les assertions sur les propriétés correspondant aux données attendues en sortie.</p>
<p style="text-align: justify;">L’étape suivante est d’ajouter des cas de tests supplémentaires. Une façon simple de le réaliser pourrait être de copier-coller le scénario précédent et d’en changer les paramètres. Mais comme toujours, nous cherchons à réduire la duplication du code (DRY). Nous allons donc utiliser ici une fonctionnalité très pratique et mettre en œuvre un “Scenario Outline”, associé à des exemples :</p>

<div style="overflow: scroll; text-align: justify;">
<pre>Scenario Outline: Filter on a single criterion
    Given I have written a query against the provider
    And I have added a &lt;predicate&gt; where clause
    Given The people finder service filters on &lt;servicePredicate&gt;
    When I execute the query
    Then The service parameter should be &lt;serviceParameter&gt;
    And The result count should be &lt;resultsCount&gt;
    And The result should validate the servicePredicate
    Examples:
    | predicate                     | servicePredicate              | serviceParameter            | resultsCount |
    | Age &gt;= 36                     | Age &gt;= 36                     | Age IsGreaterThan 36        | 3            |
    | Age &gt;= 36                     |                               | Age IsGreaterThan 36        | 6            |
    | FirstName.StartsWith("Scar")  | FirstName.StartsWith("Scar")  | FirstName StartsWith Scar   | 1            |
    | LastName == Alba              |                               | LastName Equals Alba        | 6            |</pre>
</div>
<p style="text-align: justify;">Une fois le “Scenario Outline” préparé, nous pouvons lister tous les cas à tester. Ajouter un cas de test correspondant au même scénario ne demande alors plus que l’ajout d’une unique ligne d’exemple.</p>
<p style="text-align: justify;">Si vous avez suivi attentivement ce billet jusqu’ici, vous aurez peut-être remarqué que mon scénario fait des assertions sur le nombre de résultats, mais que je n’ai défini nulle part de données de test. En fait, il y a une clause “Given” cachée, qui est utilisée pour tous les scénarios du fichier “feature” ! Pour mettre en place un tel comportement, il suffit d’utiliser le mot-clé “Background” :</p>

<pre>Background:
    Given The people are
    | FirstName | LastName    | Age | Id                                     |
    | Scarlett  | Johansson   | 27  | {8c319634-935d-4681-adcc-02d5347fe6c4} |
    | Jessica   | Alba        | 31  | {32a84597-8c3d-44bc-a1a5-6538188e9d25} |
    | Penelope  | Cruz        | 38  | {5aa0eb59-3961-472f-b829-7d54ac8eeeef} |
    | Reese     | Witherspoon | 36  | {77fcd741-3839-4692-925f-a3a0eb19cf42} |
    | Charlize  | Theron      | 36  | {e37290f6-d376-44e2-944d-d0af13c1a75c} |
    | Mouloud   | Achour      | 31  | {18af541a-a5dc-41d2-af47-479b1c06e216} |</pre>
<p style="text-align: justify;">Un dernier point : bien que l’interprétation des expressions lambdas fournies en arguments dans ce scénario soit un sujet indépendant qui pourrait être discuté plus avant, paramétrer les données de test utilisées dans la dernière clause “Given” présentée est d’une simplicité enfantine, et je n’ai pas pu résister à reproduire le code ici :</p>

<pre>[Given(@"The people are")]
public void GivenThePeopleAre(Table people)
{
    this.context.AllPeople = people.CreateSet&lt;Person&gt;();
}</pre>
<p style="text-align: justify;">Grâce aux méthodes utilitaires incluses dans SpecFlow, la création du jeu de données de test est complètement automatisée ! En conclusion, je vous recommande très fortement d’essayer SpecFlow, et je pense vraiment que vous ne le regretterez pas.</p>
<p style="text-align: justify;">Tout le code source de ce billet ainsi que celui des billets précédents de la série Linq Provider, est désormais disponible sur mon GitHub : <a title="https://github.com/pirrmann/LinqToService" href="https://github.com/pirrmann/LinqToService" target="_blank">https://github.com/pirrmann/LinqToService</a>. N’hésitez surtout pas à brancher / contribuer sur ce code, et je serais vraiment ravi de recevoir vos avis éventuels !</p>
