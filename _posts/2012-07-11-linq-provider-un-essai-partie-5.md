---
layout: post
title: "Linq provider : un essai… partie 5"
date: 2012-07-11 15:22
author: pierre irrmann
comments: true
categories: [AST, C#, CSS3, HTML, LINQ, Outils, Programmation]
---
<h3 style="text-align: justify;"><span style="color: #333333;">Améliorons la boite à outils : construisons nos propres visualisations d’arbres d’expression.</span></h3>

<p style="text-align: justify;">Dans les deux derniers billets, <a href="http://www.arolla.fr/blog/2012/06/linq-provider-un-essai%E2%80%A6-partie-3/" target="_blank">Linq provider : un essai… partie 3</a> et <a href="http://www.arolla.fr/blog/2012/07/linq-provider-un-essai%E2%80%A6-partie-4/" target="_blank">Linq provider : un essai… partie 4</a>, j’ai inclus des exemples de visualisations d’arbres d’expression. Je les ai construit en utilisant un pattern Visiteur (et oui, encore !), qui produit du markup HTML. Il s’agit ici d’un bon exemple de ce que ce pattern permet d’accomplir, et je vais donc en profiter pour expliquer son fonctionnement.</p>

<p style="text-align: justify;">La visualisation d’expression qui est générée est basée sur du code HTML / CSS3, en utilisant le code présenté à l’adresse suivante: <a href="http://thecodeplayer.com/walkthrough/css3-family-tree" target="_blank">http://thecodeplayer.com/walkthrough/css3-family-tree</a>. Pour résumer, le markup est formé de listes non ordonnées (balises “<em>&lt;ul&gt;</em>” et “<em>&lt;li&gt;</em>”) qui contiennent elles-même d’autres listes. Pour des raisons techniques, je n’ai pas pu injecter simplement le CSS nécessaire à la visualisation des arbres en HTML directement sur ce blog, mais vous pouvez regarder le code HTML généré en inspectant le DOM des deux exemples suivants : <a href="http://www.pirrmann.net/wp-content/samples/linq-provider/sample-001.html" target="_blank">arbre 1</a> et <a href="http://www.pirrmann.net/wp-content/samples/linq-provider/sample-002.html" target="_blank">arbre 2</a>.</p>

<p style="text-align: justify;">Le générateur de visualisation est une classe nommée TreeVisualizer, qui hérite de la classe ExpressionVisitor :</p>

<pre>internal class TreeVisualizer : ExpressionVisitor
{
    private StringBuilder builder;
    private HtmlTextWriter writer;

    private TreeVisualizer()
    {
        this.builder = new StringBuilder();
        this.writer = new HtmlTextWriter(
            new StringWriter(
                this.builder,
                CultureInfo.InvariantCulture),
            "    ");
    } [...]</pre>

<p style="text-align: justify;">Le constructeur de cette classe est privé, la seule manière d’utiliser la classe est par l’intermédiaire de la méthode statique BuildVisualization :</p>

<pre>internal static string BuildVisualization(Expression expression)
{
    TreeVisualizer visualizer = new TreeVisualizer();
    visualizer.Visit(expression);
    return visualizer.Visualization;
}</pre>

<p style="text-align: justify;">La propriété retournée par la méthode, Visualization, est définie comme ceci :</p>

<pre>public string Visualization
{
    get
    {
        return this.builder.ToString();
    }
}</pre>

<p style="text-align: justify;">La classe utilise également deux méthodes utilitaires :</p>

<ul style="text-align: justify;">
    <li>GetSimplifiedType, qui permet d’obtenir une représentation textuelle d’un type donné.</li>
</ul>

<pre>private string GetSimplifiedType(Type type)
{
    if (!type.IsGenericType)
        return type.Name;

    string genericName = type.Name.Split('`').First();

    string genericArguments = string.Join(
        ", ",
        type.GenericTypeArguments.Select(
            t =&gt; GetSimplifiedType(t)));

    return string.Format(
        "{0}&lt;{1}&gt;",
        genericName,
        genericArguments);
}</pre>

<ul style="text-align: justify;">
    <li>VisitAndBuildTree, qui ajoute du contenu au HtmlTextWriter, en fonction des nœuds de l’arbre visité :</li>
</ul>

<pre>private Expression VisitAndBuildTree(
    string nodeName,
    string nodeType,
    string nodeDescription,
    Func&lt;Expression&gt; childrenVisitorFunction = null)
{
    this.writer.RenderBeginTag("li");
    this.writer.WriteLine();
    this.writer.Indent++;

    this.writer.AddAttribute("href", "#");
    this.writer.RenderBeginTag("a");

    this.writer.AddAttribute("class", "node-name");
    this.writer.RenderBeginTag("span");
    this.writer.WriteEncodedText(nodeName);
    this.writer.RenderEndTag();
    this.writer.WriteBreak();

    if (!string.IsNullOrEmpty(nodeType))
    {
        this.writer.AddAttribute("class", "node-type");
        this.writer.RenderBeginTag("span");
        this.writer.WriteEncodedText(nodeType);
        this.writer.RenderEndTag();
        this.writer.WriteBreak();
    }

    this.writer.WriteEncodedText(nodeDescription);
    this.writer.RenderEndTag();
    this.writer.WriteLine();

    Expression baseReturn = null;
    if (childrenVisitorFunction != null)
    {
        this.writer.RenderBeginTag("ul");
        this.writer.WriteLine();
        this.writer.Indent++;

        baseReturn = childrenVisitorFunction();

        this.writer.Indent--;
        this.writer.RenderEndTag();
        this.writer.WriteLine();

    }

    this.writer.Indent--;
    this.writer.RenderEndTag();
    this.writer.WriteLine();

    return baseReturn;
}</pre>

<p style="text-align: justify;">Grâce aux deux fonctions précédentes, nous pouvons désormais surcharger chacune des méthodes Visit<em>SomeTypeOfNode </em>et insérer les détails appropriés dans le HTML. Par exemple :</p>

<pre>protected override Expression VisitBinary(
    BinaryExpression node)
{
    return VisitAndBuildTree(
        "Binary",
        string.Empty,
        node.NodeType.ToString(),
        () =&gt; base.VisitBinary(node));
}
protected override Expression VisitLambda&lt;T&gt;(
    Expression&lt;T&gt; node)
{
    return VisitAndBuildTree(
        "Lambda",
        GetSimplifiedType(node.Type),
        node.ToString(),
        () =&gt; base.VisitLambda&lt;T&gt;(node));
}
protected override Expression VisitMethodCall(
    MethodCallExpression node)
{
    return VisitAndBuildTree(
        "Call",
        GetSimplifiedType(node.Type),
        node.Method.Name,
        () =&gt; base.VisitMethodCall(node));
}</pre>

<p style="text-align: justify;">Il y a de nombreuses méthodes, je ne vais pas toutes les reproduire dans ce billet. Je vais juste finir avec une méthode particulière : VisitConstant. Cette méthode sert à représenter un nœud de type constante, et il est intéressant de faire figurer des informations sur la valeur de la constante contenue dans le nœud. J’ai géré deux cas particuliers, lorsque la constante est un IEnumerable, et lorsqu’il s’agit d’une chaîne de caractères.</p>

<pre>protected override Expression VisitConstant(
    ConstantExpression node)
{
    string type = GetSimplifiedType(node.Type);

    string value;
    if (node.Type.IsGenericType
        &amp;&amp; node.Type.FindInterfaces(
            (t, o) =&gt; t.Name.StartsWith("IEnumerable"),
            true).Any())
    {
        value = type;
    }
    else if(type == "String")
    {
        value = string.Concat(
            "\"",
            ((string)node.Value).Replace("\"", "\\\""),
            "\"");
    }
    else
    {
        value = node.Value.ToString();
    }

    VisitAndBuildTree(
        "Constant",
        type,
        value);
    return base.VisitConstant(node);
}</pre>

<p style="text-align: justify;">Ainsi, nous pouvons visualiser les valeurs des constantes pour les types simples dans l’arbre, et une description simplifiée du type de données lorsque la constante est un IEnumerable.</p>

<p style="text-align: justify;">Dans le prochain billet, nous nous attacherons à améliorer la testabilité du provider, à partir du feedback obtenu lors de la Jam de Code Arolla du mois de juin.</p>

<p style="text-align: justify;"></p>
