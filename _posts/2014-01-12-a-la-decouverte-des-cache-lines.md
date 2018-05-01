---
layout: post
title: A la découverte des Cache Lines
date: 2014-01-12 12:09
author: jerome prudent
comments: true
categories: [cache lines, hardware, java, mémoire, optimisation, Programmation, RAM]
---
<h2 style="text-align: justify;">Introduction</h2>

<p style="text-align: justify;">Dans cet article, je vais vous parler des performances des programmes liées aux accès à la RAM en mettant en évidence les cache lines.</p>

<p style="text-align: justify;">L'article est scindé en 3 parties: un peu de théorie, une expérience et l'interprétation de l'expérience. Le tout ne devrait pas vous prendre plus de 7 minutes de votre temps.</p>

<p style="text-align: justify;">J'utilise la microarchitecture <a href="http://fr.wikipedia.org/wiki/Sandy_Bridge">Sandy Bridge </a>d'Intel sur lequel est monté un CPU i7 2600K.</p>

<h2 style="text-align: justify;">Les mémoires</h2>

<h3 style="text-align: justify;">Les zones mémoire dédiées par core</h3>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/01/core.png"><img class="alignnone size-full wp-image-2105" alt="core" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/core.png" width="245" height="362" /></a>
Il y a 3 zones mémoires par core :</p>

<ul style="text-align: justify;">
    <li><strong>Les <a href="http://fr.wikipedia.org/wiki/Registre_de_processeur">registres</a></strong> : Ce sont une trentaine de zones mémoire faisant partie de l'<a href="http://fr.wikipedia.org/wiki/Unit%C3%A9_arithm%C3%A9tique_et_logique">ALU</a> et qui ont chacune un petit nom.
Leurs capacités varient de 32 à 256 bits.
La lecture et l'écriture dans un registre se fait en <strong>1 cycle d'horloge</strong> seulement. Toute information issue de la RAM est stockée dans un registre avant d'être manipulée.</li>
    <li><strong>Le cache de niveau 1</strong> : Cette zone mémoire est divisée en deux parties distinctes de 32Ko chacune: le cache d'instruction et le cache de données. L'accès à ce cache prend <strong>4 cycles</strong>.</li>
    <li><strong>Le cache de niveau 2</strong> : Cette zone mémoire fait 256Ko. Un accès prend <strong>11 cycles</strong>.</li>
</ul>

<h3 style="text-align: justify;">Les zones mémoire partagées par tous les cores</h3>

<p style="text-align: justify;">Ensuite, partagées par tous les cores, on a cela :</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/01/mem_big_picture.png"><img class="alignnone size-full wp-image-2106" alt="mem_big_picture" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/mem_big_picture.png" width="668" height="779" /></a></p>

<ul style="text-align: justify;">
    <li><strong>Le cache de niveau 3</strong> : Cette zone mémoire partagée fait 8Mo.
Un accès prend <strong>25 cycles</strong>.
Le cache de niveau L3 est physiquement dans le CPU.</li>
    <li><strong>La RAM</strong> : ou DRAM pour être plus précis.
Ma machine a 3 barrettes de DDR3-1066, pour un total de 12Go.
Un accès RAM est difficile à mesurer, ça dépend de plein de facteurs, variant du matériel, au nombre de threads, à la localisation des données, ...
Le truc à retenir c'est qu'on change d'unité de mesure, on passe à la <strong>nanoseconde</strong> (10e-9 seconde). Dans un ordre d'idée, c'est 10 fois plus lent que l'accès au L3.
Les accès RAM se font via le <a href="http://fr.wikipedia.org/wiki/Unit%C3%A9_de_gestion_m%C3%A9moire">MMU</a> (Memory Management Unit).</li>
    <li><strong>Le disque</strong> : ce n'est pas vraiment une zone mémoire du CPU puisque c'est le boulot de l'OS de mettre à disposition sur la RAM les données du disque.
C'est également difficile à mesurer. Là encore, on change d'unité, on passe à la <strong>milliseconde</strong>.</li>
</ul>

<p style="text-align: justify;">Le ring sert de bus de communication entre les cœurs et le cache de niveau L3 ou la RAM.</p>

<h3 style="text-align: justify;">Deux notions importantes</h3>

<ul style="text-align: justify;">
    <li>Toute information en RAM devant être traitée par le CPU doit être recopiée dans le cache L1.</li>
    <li>Toute instruction devant être exécutée passe par le cache L1.</li>
</ul>

<p style="text-align: justify;">D'un point de vue temps d'exécution, ce qu'il faut retenir c'est que plus les données sont proches du CPU, plus ça dépote !</p>

<h2 style="text-align: justify;">L'expérience</h2>

<h3 style="text-align: justify;">Le programme java</h3>

<div class="highlight" style="text-align: justify;">
<pre style="overflow: auto;"><code class="java" style="border: 0; padding: 0;">    <span class="kn" style="color: #008000; font-weight: bold;">package</span> <span class="n">jprudent</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">cacheLines</span><span class="o" style="color: #666666;">;</span>

    <span class="kd" style="color: #008000; font-weight: bold;">public</span> <span class="kd" style="color: #008000; font-weight: bold;">class</span> <span class="nc" style="color: #0000ff; font-weight: bold;">Ex2</span> <span class="o" style="color: #666666;">{</span>

        <span class="kd" style="color: #008000; font-weight: bold;">private</span> <span class="kd" style="color: #008000; font-weight: bold;">static</span> <span class="kd" style="color: #008000; font-weight: bold;">final</span> <span class="kt" style="color: #b00040;">int</span> <span class="n">size</span> <span class="o" style="color: #666666;">=</span> <span class="mi" style="color: #666666;">512</span><span class="o" style="color: #666666;">*</span><span class="mi" style="color: #666666;">1024</span><span class="o" style="color: #666666;">*</span><span class="mi" style="color: #666666;">1024</span><span class="o" style="color: #666666;">;</span>
        <span class="kd" style="color: #008000; font-weight: bold;">private</span> <span class="kd" style="color: #008000; font-weight: bold;">static</span> <span class="kd" style="color: #008000; font-weight: bold;">final</span> <span class="kt" style="color: #b00040;">long</span><span class="o" style="color: #666666;">[]</span> <span class="n">array</span> <span class="o" style="color: #666666;">=</span> <span class="k" style="color: #008000; font-weight: bold;">new</span> <span class="kt" style="color: #b00040;">long</span><span class="o" style="color: #666666;">[</span><span class="mi" style="color: #666666;">size</span><span class="o" style="color: #666666;">];</span>

        <span class="kd" style="color: #008000; font-weight: bold;">private</span> <span class="kd" style="color: #008000; font-weight: bold;">static</span> <span class="kt" style="color: #b00040;">void</span> <span class="nf" style="color: #0000ff;">benchMeLinear</span><span class="o" style="color: #666666;">(</span><span class="kt" style="color: #b00040;">int</span> <span class="n">pas</span><span class="o" style="color: #666666;">){</span>
            <span class="n">System</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">out</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">print</span><span class="o" style="color: #666666;">(</span><span class="n">pas</span> <span class="o" style="color: #666666;">+</span> <span class="s" style="color: #ba2121;">"t"</span><span class="o" style="color: #666666;">);</span>
            <span class="kt" style="color: #b00040;">long</span> <span class="n">start</span> <span class="o" style="color: #666666;">=</span> <span class="n">System</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">nanoTime</span><span class="o" style="color: #666666;">();</span>
            <span class="k" style="color: #008000; font-weight: bold;">for</span><span class="o" style="color: #666666;">(</span><span class="kt" style="color: #b00040;">int</span> <span class="n">i</span><span class="o" style="color: #666666;">=</span><span class="mi" style="color: #666666;">0</span><span class="o" style="color: #666666;">;</span> <span class="n">i</span><span class="o" style="color: #666666;">&lt;</span><span class="n">size</span><span class="o" style="color: #666666;">;</span> <span class="n">i</span><span class="o" style="color: #666666;">+=</span><span class="n">pas</span><span class="o" style="color: #666666;">){</span>
                <span class="n">array</span><span class="o" style="color: #666666;">[</span><span class="n">i</span><span class="o" style="color: #666666;">]</span> <span class="o" style="color: #666666;">=</span> <span class="n">i</span> <span class="o" style="color: #666666;">*</span> <span class="mi" style="color: #666666;">3</span><span class="o" style="color: #666666;">;</span>
            <span class="o" style="color: #666666;">}</span>
            <span class="kt" style="color: #b00040;">long</span> <span class="n">end</span> <span class="o" style="color: #666666;">=</span> <span class="n">System</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">nanoTime</span><span class="o" style="color: #666666;">();</span>
            <span class="n">System</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">out</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">println</span><span class="o" style="color: #666666;">((</span><span class="n">end</span><span class="o" style="color: #666666;">-</span><span class="n">start</span><span class="o" style="color: #666666;">)</span> <span class="o" style="color: #666666;">/</span> <span class="mi" style="color: #666666;">100000</span><span class="o" style="color: #666666;">);</span>
        <span class="o" style="color: #666666;">}</span>

        <span class="kd" style="color: #008000; font-weight: bold;">public</span> <span class="kd" style="color: #008000; font-weight: bold;">static</span> <span class="kt" style="color: #b00040;">void</span>  <span class="nf" style="color: #0000ff;">main</span><span class="o" style="color: #666666;">(</span><span class="n">String</span> <span class="o" style="color: #666666;">...</span> <span class="n">args</span><span class="o" style="color: #666666;">)</span> <span class="o" style="color: #666666;">{</span>
            <span class="n">System</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">out</span><span class="o" style="color: #666666;">.</span><span class="na" style="color: #7d9029;">println</span><span class="o" style="color: #666666;">(</span><span class="s" style="color: #ba2121;">"#Taille du tableau : "</span> <span class="o" style="color: #666666;">+</span> <span class="n">size</span><span class="o" style="color: #666666;">);</span>
            <span class="k" style="color: #008000; font-weight: bold;">for</span><span class="o" style="color: #666666;">(</span><span class="kt" style="color: #b00040;">int</span> <span class="n">i</span> <span class="o" style="color: #666666;">=</span> <span class="mi" style="color: #666666;">1</span><span class="o" style="color: #666666;">;</span> <span class="n">i</span><span class="o" style="color: #666666;">&lt;</span><span class="mi" style="color: #666666;">1024</span><span class="o" style="color: #666666;">;</span> <span class="n">i</span><span class="o" style="color: #666666;">+=</span><span class="mi" style="color: #666666;">1</span><span class="o" style="color: #666666;">){</span>
                <span class="n">benchMeLinear</span><span class="o" style="color: #666666;">(</span><span class="n">i</span><span class="o" style="color: #666666;">);</span>
            <span class="o" style="color: #666666;">}</span>
        <span class="o" style="color: #666666;">}</span>
    <span class="o" style="color: #666666;">}</span>
</code></pre>
</div>

<p style="text-align: justify;">On met en RAM un gros tableau de 512 millions de long (0,5G ✕ 8 = 4G).
Puis on le remplit linéairement N fois, en sautant N cases à chaque fois.</p>

<p style="text-align: justify;">A chaque parcours, on affiche le temps d'exécution.</p>

<p style="text-align: justify;">Au premier parcours, le pas vaut 1, le tableau est intégralement rempli.</p>

<p style="text-align: justify;">Au second, le pas vaut 2, on remplit une case sur 2.</p>

<p style="text-align: justify;">Au troisième, le pas vaut 3, on remplit une case sur 3.</p>

<p style="text-align: justify;">...</p>

<h3 style="text-align: justify;">Résultats</h3>

<p style="text-align: justify;">On exécute le programme:</p>

<p style="text-align: justify;"><code>java -Xmx8G jprudent.CacheLine.Ex2 &gt; linear.dat</code></p>

<p style="text-align: justify;">On génère un petit graphique avec gnuplot :</p>

<pre style="overflow: auto;"><code style="border: 0; padding: 0;">gnuplot&gt; set logscale x 2
gnuplot&gt; plot "linear.dat" using 1:2
</code></pre>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2014/01/linear.png"><img class="alignnone size-full wp-image-2100" alt="linear" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/linear.png" width="640" height="480" /></a></p>

<p style="text-align: justify;">Le temps de parcours est constant de 1 à 8. Si le programme parcourt toutes les valeurs ou s'il ne parcourt qu'une valeur sur 8, le résultat est identique.</p>

<p style="text-align: justify;">Après 8, le temps de parcours devient proportionnel au nombre de valeurs parcourues.</p>

<p style="text-align: justify;"><strong>Il est aussi coûteux en temps de parcourir linéairement 512 millions de valeurs que 64 millions valeurs.</strong></p>

<p style="text-align: justify;">Le nombre de fois où l'on exécute <code>i * 3</code> a un impact négligeable sur le temps d'exécution, sinon ce ne serait pas plat jusqu'à 8.
Reste le coût d'accès à la mémoire qui pourrait expliquer. <strong></strong></p>

<h3 style="text-align: justify;">Pourquoi 8 ?</h3>

<p style="text-align: justify;">J'ai mentionné que n'importe quelle information issue de la RAM doit au moins passer par le cache L1.</p>

<p style="text-align: justify;"><span style="text-decoration: underline;">Détail important</span> : les informations ne sont pas copiées octet par octet depuis la RAM vers le cache mais carrément par blocs de 64 octets contigus mis en cache. Ces blocs de 64 octets s'appellent <em>cache line</em>.</p>

<p style="text-align: justify;">Dans le cas du parcours du tableau avec un pas de 1:</p>

<ol style="text-align: justify;">
    <li>Quand <code>i = 0</code>, l'instruction <code>array[0] = 0 * 3</code> nécessite les étapes suivantes :
<ol>
    <li>On doit charger dans le cache les 8 premiers octets du tableau (1 <code>long</code>
fait 8 octets) de la RAM vers le cache L1. On va dire que c'est l'addresse 0 (@0x00)</li>
    <li>en fait ce sont 64 octets qui sont copiés de la RAM vers le cache,
la cache line couvrant les adresses 0x00 à 0x3F.</li>
    <li>affecter la valeur 0 à @0x00</li>
</ol>
</li>
    <li>Quand <code>i = 1</code>, l'instruction <code>array[1] = 1 * 3</code> nécessite les étapes suivantes :
<ol>
    <li>L'adresse @0x08 est déjà dans le cache (on l'a chargée en 1.1)</li>
    <li>affecter la valeur 3 à @0x08</li>
</ol>
</li>
    <li>...</li>
    <li>Quand <code>i = 7</code>, l'instruction <code>array[7] = 7 * 3</code> nécessite les étapes suivantes :
<ol>
    <li>L'adresse @0x38 est déjà dans le cache (on l'a chargée en 1.1)</li>
    <li>affecter la valeur 21 à @0x38</li>
</ol>
</li>
    <li>Quand <code>i = 8</code>, l'instruction <code>array[8] = 8 * 3</code> nécessite les étapes suivantes :
<ol>
    <li>On doit charger l'adress 0x40 pour écrire dedans. Or, cette zone
n'est pas encore remontée de la RAM</li>
    <li>en fait ce sont 64 octets qui sont copiés de la RAM vers le cache,
la cache line couvrant les adresses 0x40 à 0x79.</li>
    <li>affecter la valeur 24 à @0x40</li>
</ol>
</li>
</ol>

<p style="text-align: justify;">Un long fait 8 octets. Une cache line fait 64 octets, donc on a besoin d’accéder à la RAM tous les 8 indices seulement. Une fois qu'un indice est accessible dans le cache, les 7 suivants sont aussi accessibles quasiment gratuitement.</p>

<p style="text-align: justify;">Dans le cas où le pas vaut entre 1 et 8, il faudra monter de la RAM vers le cache l'intégralité du tableau. Et puisque monter des infos de la RAM vers le cache prend 99% du temps, pour un pas de 1 à 8, le temps d'exécution est quasiment identique.
Pour les autres cas, certaines parties du tableau ne sont jamais montées en cache. D'où le rapport <code>temps / valeur du pas</code>.</p>

<h3 style="text-align: justify;">Conclusion</h3>

<ul style="text-align: justify;">
    <li>Ce qui est lent ce ne sont pas les calculs, ce sont les accès mémoire.</li>
    <li>En comprenant le mécanisme de cache line on peut améliorer les performances d'un programme en réordonnant les données d'un programme en profitant du fait qu'on charge les données par bloc de 64k dans le cache L1.</li>
    <li>En extrapolant, on peut affirmer que le <a href="https://docs.google.com/file/d/0ByOwmqah_nuGNHEtcU5OekdDMkk/edit">Single Responsability Principle</a> n'est pas seulement un principe de design. En encapsulant des données sémantiquement proches, on a de grandes chances pour qu'elles se retrouvent dans la même cache line. Les calculs effectués en les combinant n'induisent pas de surcoût d'accès mémoire. C'est une bonne ligne de conduite pour de bonnes performances.</li>
</ul>

<h2 style="text-align: justify;">Références, inspirations et aller plus loin</h2>

<ul>
    <li style="text-align: justify;"><a href="http://igoro.com/archive/gallery-of-processor-cache-effects/">Des expérience sur les caches</a> par Igor Ostrovsky: article dont je me suis bien inspiré. Un must read si le sujet vous intéresse.</li>
    <li style="text-align: justify;"><a href="http://lwn.net/Articles/250967/">What every programmer should know about memory</a> d'Ulrich Drepper. Une série d'articles très techniques pour en apprendre davantage sur la mémoire.</li>
    <li style="text-align: justify;">La plateforme de trading haute performance <a href="http://lmax-exchange.github.io/disruptor/">LMAX</a> utilise de telles optimisations. Mr Fowler <a href="http://martinfowler.com/articles/lmax.html">en parle largement</a>, et vous devriez trouver de nombreux pointeurs chez Novencia.</li>
</ul>
