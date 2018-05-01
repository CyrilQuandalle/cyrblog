---
layout: post
title: "Linq provider : un essai… partie 3"
date: 2012-06-26 11:02
author: pierre irrmann
comments: true
categories: [AST, C#, LINQ, Outils, Programmation]
---
<h3 style="text-align: justify;"><span style="color: #333333;">Faisons enfin quelque chose !</span></h3>

<p style="text-align: justify;">Dans le <a href="http://www.arolla.fr/blog/2012/06/linq-provider-un-essai%E2%80%A6-partie-2/" target="_blank">post précédent de cette série</a> , j’ai introduit les premières classes impliquées dans la mise en place d’un provider Linq : <em>QueryableDummyData</em> et <em>DummyQueryProvider</em>. Mais ces deux classes ne prenaient pas vraiment en charge le traitement de la requête Linq. Nous en arrivions justement à la classe <em>DummyQueryContext</em>… qui, je l’annonçais, va vraiment faire… quelque chose.</p>

<p style="text-align: justify;"><em>DummyQueryContext</em> est une classe interne, qui n’expose qu’une unique méthode :</p>

<pre>internal static object Execute(Expression expression,
                               bool isEnumerable)</pre>

<p style="text-align: justify;">Le but de cette méthode est de prendre un arbre d’expression en entrée, et de retourner les résultats attendus par la requête que constitue cet arbre. Jusqu’à présent, la chaîne des appels ressemble à ceci :</p>

<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/05/linq-provider-schema-003.png" alt="" /></p>

<p style="text-align: justify;">La première implémentation de la méthode Execute est donc la suivante :</p>

<pre>internal static object Execute(Expression expression,
                                bool isEnumerable)
{
    // D’abord, on appelle le web service et récupère
    // un tableau de personnes
    IEnumerable&lt;Person&gt; people;

    using (DummyService.PeopleFinderClient service =
        new DummyService.PeopleFinderClient())
    {
        people = service.FindPeople(new DummyService.SearchCriteria());
    }

    // On utilise Ling to Objects pour récupérer une instance IQueryable
    // de personnes
    var queryablePeople = people.AsQueryable();

    // On transforme l'arbre d'expression
    Expression finalExpressionTree =
        ExpressionTreeConstantReplacer
            .CopyAndReplace(
                expression,
                typeof(QueryableDummyData&lt;Person&gt;),
                queryablePeople);

    // Finalement, en se basant sur le nouvel arbre, soit on crée
    // une requête soit on exécute avec le provider Linq to Objects
    IQueryProvider provider = queryablePeople.Provider;
    if (isEnumerable)
        return provider.CreateQuery(finalExpressionTree);
    else
        return provider.Execute(finalExpressionTree);
}</pre>

<p style="text-align: justify;">Rappelez-vous : mon cas de test consiste simplement à obtenir un énumérateur, et à vérifier que l’on peut itérer une fois. Pour faire en sorte que ce test passe, nous n’avons pas à nous soucier de quelque façon de filtrer ou trier les données renvoyées, donc l’implémentation précédente appelle le web service sans lui fournir d’argument particulier, et le service renvoie toutes ses données.</p>

<p style="text-align: justify;">A présent, la partie la plus délicate est la transformation de l’arbre d’expression. Son but est de construire un nouvel arbre d’expression dans lequel l’instance de <em>QueryableDummyData&lt;Person&gt;</em> est remplacée par le tableau <em>Person[]</em> retourné par l’appel du web service. Une fois ce remplacement effectué, la méthode Execute va laisser le provider Linq to Objects jouer la requête par rapport au tableau. Le paramètre <em>isEnumerable</em> est utilisé pour savoir si la méthode renvoie une valeur scalaire (via la méthode queryablePeople.Provider.Execute) ou un IEnumerable (via la méthode queryablePeople.Provider.CreateQuery). Pour notre premier test, nous voulons obtenir un IEnumerable&lt;Person&gt;.</p>

<p style="text-align: justify;">L’arbre d’expression manipulé est construit à partir de l’extrait de code suivant :</p>

<pre>var queryablePeople =
    new QueryableDummyData&lt;Person&gt;();</pre>

<p style="text-align: justify;">Voici une représentation de l’arbre d’expression généré :</p>

<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/06/linq-provider-tree-001.png" alt="" /></p>

<p style="text-align: justify;">Et une représentation de l’arbre d’expression final, une fois que le <em>QueryableDummyData&lt;Person&gt;</em> a été remplacé par le tableau <em>Person[]</em> récupéré depuis le service :</p>

<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/06/linq-provider-tree-002.png" alt="" /></p>

<p style="text-align: justify;">Dans <a href="http://www.arolla.fr/blog/2012/07/linq-provider-un-essai%E2%80%A6-partie-4/" target="_blank">l'article suivant</a>, nous parlerons du pattern Visitor, et nous verrons comment la classe <em>ExpressionTreeModifier</em> effectue ce remplacement..</p>
