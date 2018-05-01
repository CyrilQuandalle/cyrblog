---
layout: post
title: "Linq provider : un essai… partie 4"
date: 2012-07-06 15:23
author: pierre irrmann
comments: true
categories: [AST, C#, LINQ, Outils, Programmation]
---
<h3 style="text-align: justify;"><a href="http://www.arolla.fr/blog/2012/06/linq-provider-un-essai%E2%80%A6-partie-3/" target="_blank">Post précédent de la série.</a></h3>

<h3 style="text-align: justify;"><span style="color: #333333;">Utilisons le pattern Visiteur pour manipuler des arbres d’expression.</span></h3>

<p style="text-align: justify;">Dans les billets de cette série, j’ai commencé à décrire une implémentation partielle d’un provider Linq. L’objectif de ce provider est de récupérer des données depuis un web service, et pour réussir à renvoyer ces données à l’appelant du provider, des manipulations d’arbres d’expression sont nécessaires. Après que nous ayons obtenu les données depuis le service, nous voulons injecter dans l’arbre d’expression représentant la requête, une référence vers ces données récupérées, à la place de l’expression représentant l’instance de <em>QueryableDummyData&lt;Person&gt;</em>, qui au final n’est qu’un marqueur de position, un “placeholder” au sein de cet arbre.</p>

<p style="text-align: justify;">Afin de remplacer ce <em>QueryableDummyData&lt;Person&gt;</em> par un tableau de <em>Person[]</em>, nous utilisons une classe nommée <em>ExpressionTreeConstantReplacer</em>, qui dérive de la classe ExpressionVisitor, intégrée nativement danse le Framework .NET 4.0, et qui elle-même est basée sur le pattern Visiteur.</p>

<p style="text-align: justify;">Appliqué aux arbres d’expression, le pattern Visiteur peut être utilisé pour générer un nouvel arbre d’expression à partir de l’arbre visité, pour accumuler de la donnée au cours de la visite de l’arbre, ou encore pour faire les deux en même temps. Chaque surcharge méthode “Visit” retourne une expression, qui est par défaut l’expression non modifiée qu’elle vient juste de visiter. Une méthode surchargée peut renvoyer un nouvel arbre, mais ne doit pas modifier l’expression visitée. Cette approche a l’avantage de ne pas avoir d’effet de bord sur l’arbre d’expression visité.</p>

<p style="text-align: justify;">Pour exemple, le pattern Visiteur peut être utilisé pour transformer l’arbre suivant (cliquez pour accéder à l’exemple HTML) :</p>

<p style="text-align: justify;" align="center"><a href="http://www.pirrmann.net/wp-content/samples/linq-provider/sample-001.html" target="_blank"><img src="http://www.pirrmann.net/wp-content/uploads/2012/06/linq-provider-schema-004.png" alt="" width="550" height="357" /></a></p>

<p style="text-align: justify;">Dans ce nouvel arbre, qui est identique à ceci près que la clause where p =&gt; p.Age &gt;= 21 a été supprimé :</p>

<p style="text-align: justify;" align="center"><a href="http://www.pirrmann.net/wp-content/samples/linq-provider/sample-002.html" target="_blank"><img src="http://www.pirrmann.net/wp-content/uploads/2012/06/linq-provider-schema-005.png" alt="" width="340" height="357" /></a></p>

<p style="text-align: justify;">La classe <em>ExpressionVisitor</em> est conçue pour être utilisée comme classe de base à des classes de manipulation d’arbres. Lorsque la méthode Visit de la classe <em>ExpressionVisitor</em> est appelée, elle détermine le type du nœud passé en paramètre, et visite récursivement le nœud et tous ses enfants, en utilisant à chaque fois la méthode adaptée au type du nœud concerné.</p>

<p style="text-align: justify;">Cela permet au développeur de la classe dérivée de surcharger uniquement les méthodes nécessaires. Par exemple, puisque nous voulons ici remplacer une noeud de type “Constante” par un autre, la classe <em>ExpressionTreeConstantReplacer</em> dérive de <em>ExpressionVisitor</em> et surcharge la méthode VisitConstant.</p>

<pre>internal class ExpressionTreeConstantReplacer&lt;TReplacement&gt;
    : ExpressionVisitor
{
    private Type originalType;
    private TReplacement replacementConstant;

    internal ExpressionTreeConstantReplacer(
        Type originalType, TReplacement replacementConstant)
    {
        this.originalType = originalType;
        this.replacementConstant = replacementConstant;
    }

    protected override Expression VisitConstant(
        ConstantExpression c)
    {
        if (c.Type == this.originalType)
            return Expression.Constant(this.replacementConstant);
        else
            return c;
    }
}</pre>

<p style="text-align: justify;">Le constructeur de cette classe prend deux arguments : le type de la constante d’origine à remplacer, et la nouvelle valeur à injecter. Par la suite, durant le processus de visite de l’arbre, lorsqu’un nœud de type “Constante” est détecté, et si son type correspond au type à remplacer, alors un nouveau nœud de type “Constante”, contenant la valeur de remplacement, est retourné à la place du nœud de départ.</p>

<p style="text-align: justify;">Afin de rendre la syntaxe des appels de remplacement plus expressive, une classe utilitaire a aussi été introduite, pour tirer parti de l’inférence de type de C#, qui est disponible sur les appels de méthodes statiques (et donc d’extension), mais pas sur les constructeurs :</p>

<pre>internal class ExpressionTreeConstantReplacer
{
    internal static Expression CopyAndReplace&lt;TReplacement&gt;(
        Expression expression,
        Type originalType,
        TReplacement replacementConstant)
    {
        var modifier =
            new ExpressionTreeConstantReplacer&lt;TReplacement&gt;(
                originalType,
                replacementConstant);
        return modifier.Visit(expression);
    }
}</pre>

<p style="text-align: justify;">Cela nous permet d’écrire désormais le remplacement de la manière suivante :</p>

<pre>Expression finalExpressionTree =
    ExpressionTreeConstantReplacer
        .CopyAndReplace(
            filteredExpressionTree,
            typeof(QueryableDummyData&lt;Person&gt;),
            queryablePersons);</pre>

<p style="text-align: justify;">Dans <a href="http://www.arolla.fr/blog/2012/07/linq-provider-un-essai%E2%80%A6-partie-5/" target="_blank">le prochain billet</a>, je décrirai la classe TreeVisualizer que j’ai écrite afin de générer les visualisations d’arbres d’expression présentes dans ce post.</p>
