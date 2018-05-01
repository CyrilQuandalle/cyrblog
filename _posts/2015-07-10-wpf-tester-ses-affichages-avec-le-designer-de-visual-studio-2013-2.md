---
layout: post
title: "WPF : Tester ses affichages avec le designer de Visual Studio (2013)"
date: 2015-07-10 10:30
author: vincent bourgeois
comments: true
categories: [C#, editeur, outils, Outils, Programmation, programming, visualization]
---
<p style="text-align: left;">Vous développez des interfaces graphiques complexes, mais pour tester l’affichage, il faut absolument démarrer l’application, et ça, ça peut prendre du temps pour accéder à l’interface voulue ? Pas de problème, Visual Studio permet d’afficher des valeurs par défaut dans le designer !
Je vais partir d’un exemple simple : un ViewModel composé d’une liste de Models composés eux même de 2 champs.</p>

<img class="aligncenter" src="http://tof.canardpc.com/view/e4d93f8c-a62d-4da7-bbc7-5a2f822d5f11.jpg" alt="" />

<p style="text-align: center;"><i>On remerciera le générateur de classes de Visual studio de clarifier la situation</i></p>

<p style="text-align: left;">Je vais représenter ça dans ma fenêtre principale sous la forme d’une ListView.
Pour cela, j’ouvre mon application avec legacy important préférée :
<img class=" aligncenter" title="Spoiler : en fait c'est une démo" src="http://tof.canardpc.com/view/a81548f8-a562-48e3-9002-dc5d3b848bb7.jpg" alt="" /></p>

<p style="text-align: center;"><i>Spoiler : c'est une démo</i></p>

<span style="text-decoration: underline;">Étape 1</span> : Faire le lien entre notre ViewModel et notre Vue. Pour cela, on définit dans le XAML le namespace du ViewModel, puis on l’inscrit en tant que DataContext.
<code>&lt;Window x:Class="demoWPF.MainWindow"
xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:Controller="clr-namespace:demoWPF"
Title="MainWindow" Height="350" Width="525"&gt;
&lt;Window.DataContext&gt;
&lt;Controller:DemoViewModel/&gt;
&lt;/Window.DataContext&gt;</code>
Effet sympa, avec le lien créé en étape 1, on débloque l’auto-complétion qui se fait sur les propriétés du DataContext dans le XAML :
<img class=" aligncenter" src="http://tof.canardpc.com/view/0f992c8d-ba50-4f72-bae1-02150238c1b9.jpg" alt="" />

<p style="text-align: center;"><i>Productivité : over 9000</i></p>

<p style="text-align: left;">Effet sympas numéro 2 : ça marche aussi pour les UserControls :)</p>

<p style="text-align: left;"><span style="text-decoration: underline;">Étape 2:</span> On remplit les informations par défaut du ViewModel. Autre astuce, si vous ne voulez pas que ces informations polluent votre code, ajoutez le test de détection si vous êtes dans le designer comme suit :
<code>public DemoViewModel()
{
if(DesignerProperties.GetIsInDesignMode(new DependencyObject()))
{
models = new List&lt;DemoModel&gt;() { new DemoModel(),new DemoModel()};
}
}</code></p>

<p style="text-align: left;"><span style="text-decoration: underline;">Étape3 :</span> On remplit les informations de base du Model.
<code>public DemoModel()
{
StrField1 = "field1";
StrField2 = "field2";
}</code></p>

<p style="text-align: left;"><span style="text-decoration: underline;">Étape 4 : </span>On affiche dans une ListView.
<code>&lt;ListView ItemsSource="{Binding models}" Grid.ColumnSpan="2"&gt;
&lt;ListView.View&gt;
&lt;GridView&gt;
&lt;GridViewColumn Header="Header1" DisplayMemberBinding="{Binding StrField1}"/&gt;
&lt;GridViewColumn Header="Header2" DisplayMemberBinding="{Binding StrField2}"/&gt;
&lt;/GridView&gt;
&lt;/ListView.View&gt;
&lt;/ListView&gt;</code></p>

<p style="text-align: left;"><span style="text-decoration: underline;">Étape 5 :</span> On observe le résultat.
<img class=" aligncenter" title="Santé, sobriété" src="http://tof.canardpc.com/view/02a131ec-d8b7-40b6-a2f7-b14155890289.jpg" alt="" /></p>

<p style="text-align: center;"><i>Santé, sobriété</i></p>

Cet exemple était très basique, mais il permet de visualiser rapidement l’aspect de l’affichage.
<span style="text-decoration: underline;">Étape 6 : aller plus loin.</span> Il ne faut pas s’arrêter là, cette technique permet de tester l’affichage basique de composants complexes. Il est possible aussi de tester ainsi de façon presque unitaire les différents cas d’affichage. Et qui a dit qu’on ne pouvait pas utiliser de mock plutôt que ton vrai ViewModel, et ainsi éviter de mélanger le code de l’objet qui part en PROD avec le code d’affichage ?
