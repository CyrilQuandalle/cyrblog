---
layout: post
title: Présentation de NBuilder
date: 2015-03-09 08:55
author: didier cauvin
comments: true
categories: [Nbuilder, nfluent, Outils, Programmation]
---
<h1>Introduction</h1>

NBuilder est une bibliothèque qui permet de créer des objets pour vos tests de manière rapide et fluide grâce à son interface "fluent" (<a title="Présentation de NFluent" href="http://www.arolla.fr/blog/2014/07/presentation-de-nfluent/" target="_blank">un peu à la manière de NFluent</a>). Je vais tenter au travers de cet article de vous en présenter les bases.

(NBuilder est disponible via nuget. Vous pouvez donc l'installer via l'interface de Visual Studio ou à coup de Install-Package NBuilder)

<h1>1 - Premiers pas</h1>

Commençons par nous concocter une petite classe "Product", qui nous servira tout au long de l'article. Définissons un produit comme ayant les propriétés suivantes :
<code>
public class Product
{
public int Id { get; set; }
public string Name { get; set; }
public string BrandName { get; set; }
public double Price { get; set; }
public int Stock { get; set; }
}
</code>

NBuilder propose deux méthodes de création :

<ul>
    <li>Une méthode de création de liste : CreateListOfSize</li>
    <li>Une méthode de création d'objet : CreateNew</li>
</ul>

Commençons par créer un produit. Voici comment faire avec NBuilder :
<code>
[TestMethod]
public void CreateProduct()
{
var product = Builder
.CreateNew()
.Build();
Check.That(product).IsNotNull();
}
</code>

Un petit coup de NFluent pour vérifier que notre produit a bien été instancié et voilà le travail!

Mais regardons d'un peu plus prés l'objet fraîchement créé. En effet, comme nous n'avons à aucun moment initialisé les propriétés de notre produit, on peut se demander ce qu'il contient. Voici le résultat :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "BrandName1",
"Price": 1,
"Stock": 1
}]

NBuilder les a initialisées pour nous. Plutôt pratique non?

Poussons l'exercice un peu plus loin. Imaginons maintenant que nous ayons besoin de créer un produit sur une marque en particulier. Voici comment résoudre le problème avec NBuilder :
<code>
[TestMethod]
public void CreateProduct()
{
var product = Builder
.CreateNew()
.With(x =&gt; x.BrandName = "FooBrand")
.Build();
Check.That(product.BrandName).IsEqualTo("FooBrand");
}
</code>

On obtient donc :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "FooBrand",
"Price": 1,
"Stock": 1
}]

Si on veut initialiser un produit avec un nom et une marque, on ferait :
<code>
[TestMethod]
public void CreateProduct()
{
var product = Builder
.CreateNew()
.With(x =&gt; x.Name = "MyProduct")
.And(x =&gt; x.BrandName = "FooBrand")
.Build();
Check.That(product.Name).IsEqualTo("MyProduct");
Check.That(product.BrandName).IsEqualTo("FooBrand");
}
</code>

On a donc :

[{
"Id": 1,
"Name": "MyProduct",
"BrandName": "FooBrand",
"Price": 1,
"Stock": 1
}]

Le résultat ne vous surprendra sûrement pas. Vous avez compris le principe je suppose! ;)

<h1>2 - Les listes</h1>

Imaginons que nous voulions, pour les besoins d'un test, créer une liste de 5 produits. Avec NBuilder, rien de plus simple :
<code>
[TestMethod]
public void CreateListOf5Products()
{
var products = Builder
.CreateListOfSize(5)
.Build();
Check.That(products).HasSize(5);
}
</code>

Résultat :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "BrandName1",
"Price": 1,
"Stock": 1
}, {
"Id": 2,
"Name": "Name2",
"BrandName": "BrandName2",
"Price": 2,
"Stock": 2
}, {
"Id": 3,
"Name": "Name3",
"BrandName": "BrandName3",
"Price": 3,
"Stock": 3
}, {
"Id": 4,
"Name": "Name4",
"BrandName": "BrandName4",
"Price": 4,
"Stock": 4
}, {
"Id": 5,
"Name": "Name5",
"BrandName": "BrandName5",
"Price": 5,
"Stock": 5
}]

Sans surprise, NBuilder a initialisé les propriétés automatiquement.

Créons maintenant un jeu de test avec des produits issus d'une même marque. En utilisant la méthode All() puis en appliquant à la propriété "Name" la valeur "FooBrand" avec la méthode "With", on obtient le code suivant :
<code>
[TestMethod]
public void CreateListOf5ProductsWithSameBrandName()
{
var products = Builder
.CreateListOfSize(5)
.All()
.With(x =&gt; x.BrandName = "FooBrand")
.Build();
var brandNames = products.Select(x =&gt; x.BrandName).Distinct();
Check.That(brandNames).HasSize(1);
}
</code>

Si on examine les propriétés, on voit bien que seule la propriété "BrandName" a été initialisée comme nous le souhaitions :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "FooBrand",
"Price": 1,
"Stock": 1
}, {
"Id": 2,
"Name": "Name2",
"BrandName": "FooBrand",
"Price": 2,
"Stock": 2
}, {
"Id": 3,
"Name": "Name3",
"BrandName": "FooBrand",
"Price": 3,
"Stock": 3
}, {
"Id": 4,
"Name": "Name4",
"BrandName": "FooBrand",
"Price": 4,
"Stock": 4
}, {
"Id": 5,
"Name": "Name5",
"BrandName": "FooBrand",
"Price": 5,
"Stock": 5
}]

Évidemment, comme pour l'instanciation d'un objet, on peut initialiser autant de propriétés qu'on veut dans une liste :
<code>
[TestMethod]
public void CreateListOf5ProductsWithSameBrandNameAndPrice()
{
var products = Builder
.CreateListOfSize(5)
.All()
.With(x =&gt; x.BrandName = "FooBrand")
.And(x =&gt; x.Price = 50.0d)
.Build();
var subs = products.Select(x =&gt; new { x.Price, x.BrandName }).Distinct();
Check.That(subs).HasSize(1);
}
</code>

Et voici la représentation de la liste :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "FooBrand",
"Price": 50,
"Stock": 1
}, {
"Id": 2,
"Name": "Name2",
"BrandName": "FooBrand",
"Price": 50,
"Stock": 2
}, {
"Id": 3,
"Name": "Name3",
"BrandName": "FooBrand",
"Price": 50,
"Stock": 3
}, {
"Id": 4,
"Name": "Name4",
"BrandName": "FooBrand",
"Price": 50,
"Stock": 4
}, {
"Id": 5,
"Name": "Name5",
"BrandName": "FooBrand",
"Price": 50,
"Stock": 5
}]

On peut aussi tout à fait initialiser des valeurs différentes entre les objets de la liste :
<code>
[TestMethod]
public void CreateListOf5ProductsWithSameBrandNamesAndStockForFirst2Elements()
{
var products = Builder
.CreateListOfSize(5)
.TheFirst(2)
.With(x =&gt; x.BrandName = "FooBrand")
.And(x =&gt; x.Stock = 3)
.TheLast(3)
.With(x =&gt; x.BrandName = "MikeBrand")
.And(x =&gt; x.Stock = 0)
.Build();
var subs = products.Select(x =&gt; new { x.Stock, x.BrandName }).Distinct();
Check.That(subs).HasSize(2);
}
</code>

Ici, j'ai demandé à NBuilder de me créer une liste de 5 produits initialisés comme suit :

<ul>
    <li>Pour les deux premiers produits, initialiser avec comme nom de marque "FooBrand" et un stock de 3</li>
    <li>Pour les trois derniers, initialiser avec comme nom de marque "MikeBrand" et un stock de 0</li>
</ul>

On obtient donc :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "FooBrand",
"Price": 1,
"Stock": 3
}, {
"Id": 2,
"Name": "Name2",
"BrandName": "FooBrand",
"Price": 2,
"Stock": 3
}, {
"Id": 3,
"Name": "Name3",
"BrandName": "MikeBrand",
"Price": 3,
"Stock": 0
}, {
"Id": 4,
"Name": "Name4",
"BrandName": "MikeBrand",
"Price": 4,
"Stock": 0
}, {
"Id": 5,
"Name": "Name5",
"BrandName": "MikeBrand",
"Price": 5,
"Stock": 0
}]

<h1>3 - Les constructeurs de l'extrême</h1>

NBuilder permet aussi d'instancier un objet via son constructeur. Pour se faire, rajoutons à notre classe "Product" un constructeur par défaut ne prenant pas de paramètre, et un autre en prenant trois : un nom de produit, un nom de marque et un prix :
<code>
public Product()
{
}</code>

public Product(string name, string brandName, double price)
{
Name = name;
BrandName = brandName;
Price = price;
}

Pour instancier un produit via son constructeur, NBuilder propose la méthode WithConstructor, qui s'utilise comme suit :
<code>
[TestMethod]
public void CreateProductWithConstructor()
{
var product = Builder
.CreateNew()
.WithConstructor(() =&gt; new Product("MyProduct", "FooBrand", 90.0d))
.Build();
Check.That(product.Name).IsEqualTo("MyProduct");
Check.That(product.BrandName).IsEqualTo("FooBrand");
Check.That(product.Price).IsEqualTo(90.0d);
}
</code>

Ce qui donne :

[{
"Id": 1,
"Name": "MyProduct",
"BrandName": "FooBrand",
"Price": 90,
"Stock": 1
}]

On peut évidemment appliquer ceci aux listes. Par exemple, admettons que je veuille créer deux produits avec une même marque et trois autres avec une autre, je ferais comme ceci :
<code>
[TestMethod]
public void CreateListOf5ProductsWithConstructor()
{
var products = Builder
.CreateListOfSize(5)
.TheFirst(2)
.WithConstructor(() =&gt; new Product("FooBrand"))
.TheLast(3)
.WithConstructor(() =&gt; new Product("BarBrand"))
.Build();
var subs = products.Select(x =&gt; x.BrandName).Distinct();
Check.That(subs).HasSize(2);
}
</code>

J'aurais bien sûr au préalable ajouté le constructeur suivant :
<code>
public Product(string brandName)
{
BrandName = brandName;
}
</code>

On obtient :

[{
"Id": 1,
"Name": "Name1",
"BrandName": "FooBrand",
"Price": 1,
"Stock": 1
}, {
"Id": 2,
"Name": "Name2",
"BrandName": "FooBrand",
"Price": 2,
"Stock": 2
}, {
"Id": 3,
"Name": "Name3",
"BrandName": "BarBrand",
"Price": 3,
"Stock": 3
}, {
"Id": 4,
"Name": "Name4",
"BrandName": "BarBrand",
"Price": 4,
"Stock": 4
}, {
"Id": 5,
"Name": "Name5",
"BrandName": "BarBrand",
"Price": 5,
"Stock": 5
}]

<h1>4 - Étendre les possibilités</h1>

A l'instar de NFluent, on peut aussi étendre les fonctionnalités de NBuilder. Supposons que je veuille créer mes objets "Product" avec par défaut leur propriété "Name" de la forme "MyProduct_{id}", je ferais comme ceci :

<code>
public static class NBuilderExtensions
{
public static IOperable WithCustomProductName(this IOperable operable)
{
((IDeclaration)operable).ObjectBuilder.With(x =&gt; x.Name = "MyProduct_" + x.Id);
return operable;
}
}
</code>

Ainsi, dans le code, cela se traduit de la manière suivante :
<code>
[TestMethod]
public void CreateListOf5ProductsWithCustomNames()
{
var products = Builder
.CreateListOfSize(5)
.All()
.WithCustomProductName()
.Build();
Check.That(products.Extracting("Name")).ContainsExactly("MyProduct_1", "MyProduct_2", "MyProduct_3", "MyProduct_4", "MyProduct_5");
}
</code>

Et ainsi nous obtenons :

[{
"Id": 1,
"Name": "MyProduct_1",
"BrandName": "BrandName1",
"Price": 1,
"Stock": 1
}, {
"Id": 2,
"Name": "MyProduct_2",
"BrandName": "BrandName2",
"Price": 2,
"Stock": 2
}, {
"Id": 3,
"Name": "MyProduct_3",
"BrandName": "BrandName3",
"Price": 3,
"Stock": 3
}, {
"Id": 4,
"Name": "MyProduct_4",
"BrandName": "BrandName4",
"Price": 4,
"Stock": 4
}, {
"Id": 5,
"Name": "MyProduct_5",
"BrandName": "BrandName5",
"Price": 5,
"Stock": 5
}]

Vous l'aurez compris, les possibilités sont nombreuses. Ainsi, je vous invite à fouiller par vous-même pour vous rendre compte à quel point NBuilder peut vous faire gagner bien du temps.

<h1>5 - Conclusion</h1>

En conclusion, je vous recommande fortement cette petite bibliothèque qui peut être au passage un bon complément à NFluent si vous souhaitez écrire des tests en mode "fluent" ;) Elle est facile d'accès et permet rapidement de se construire des jeux de données sans le moindre mal. Evidemment, je n'ai pas abordé toutes les fonctionnalités. Pour cela, je vous invite à vous rendre ici pour de plus amples informations : <a href="https://code.google.com/p/nbuilder/wiki/Overview_HowTo" target="_blank">https://code.google.com/p/nbuilder/wiki/Overview_HowTo</a>. Et le github qui va bien : <a href="https://github.com/garethdown44/nbuilder" target="_blank">https://github.com/garethdown44/nbuilder</a>

Happy testing!
