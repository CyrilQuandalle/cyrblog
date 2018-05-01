---
layout: post
title: Pourquoi s'intéresser aux graph-databases ?
date: 2013-10-22 10:29
author: fabien maury
comments: true
categories: [database, graphDatabase, graphe, neo4j, NOSQL, Outils, Programmation]
---
<p style="text-align: justify;">Les bases de données orientées graphe, vous en avez sans doute entendu parler, mais votre todo regorge de "trucs cool à essayer", alors pourquoi s'intéresser aux graph-databases dès aujourd'hui ?</p>

<h2 style="text-align: justify;">Vous avez dit Graphe ?</h2>

<p style="text-align: justify;">Les bases de données orientées graphe sont des bases de donnée <strong>NoSql</strong>, sans schéma, dont la plupart des implémentations sont <a href="http://fr.wikipedia.org/wiki/Propri%C3%A9t%C3%A9s_ACID">ACID</a>.</p>

<p style="text-align: justify;">Les objets stockés sont uniquement des <strong>nœuds</strong> (objets), des <strong>relations</strong> et des <strong>propriétés</strong>, ces dernières étant portées par les nœud et les relations.</p>

<p style="text-align: justify;">Leur force réside en la connaissance à chaque nœud du pointeur physique menant à ses nœuds voisins au lieu de le retrouver en parcourant un index via son identifiant, rendant donc les <strong>recherches locales</strong> très rapides, entraînant ainsi un gain de performance dès lors que la recherche touche des données connexes.<!--more--></p>

<p style="text-align: justify;">Seul le premier nœud d'où part la recherche est recherché en parcourant l'index. En effet chaque recherche est un parcours partant d'un nœud.</p>

<p style="text-align: justify;"><img alt="graph database model exemple" src="http://www.arolla.fr/blog/wp-content/uploads/2013/09/graph_example.png" /></p>

<p style="text-align: justify;">Ici le nœud me représentant est lié par la relation [employé de] au nœud Arolla.
Les nœuds et les relations ont ici des propriétés.</p>

<p style="text-align: justify;">Les nœuds ne sont pas typés: c'est l'effet <strong>schema-less.</strong> En effet un nœud est avant tout définit par sa disposition vis à vis d'une relation. <a href="http://www.neo4j.org/">Neo4J</a> n'avait par exemple pas de notion de type sur ses nœuds bien que soit introduit la notion de label dans la version 2.0.</p>

<h2 style="text-align: justify;">Nos données ont changé</h2>

<p style="text-align: justify;">Les bases de données relationnelles ont été créées à une époque où les applications servaient principalement à afficher des listes d'objets et à faire des opération de <a href="http://fr.wikipedia.org/wiki/CRUD">CRUD</a> sur ces dernières. Stocker ses données dans des tables structurées était alors la meilleure solution, et aujourd'hui encore la plupart d'entre nous s'en sortent très bien avec ces technologies.</p>

<p style="text-align: justify;">Cependant, non seulement le <strong>volume</strong> des données que nous devons traiter ne cesse de croître, mais elles ont aussi un besoin croissant d'être <strong>connectées</strong> entres elles. Car plus que la donnée elle-même, c'est souvent sa relation avec les autres données qui a de la valeur. L'accumulation de données de liaison permet de faire des recherches par motif et ainsi allier analyse comportementale et prédictibilité (impact d'une publicité, recommandations, etc).</p>

<p style="text-align: justify;">Malgré l'évolution des bases de données relationnelles, un modèle où le temps d'une requête dépend du nombre de données de même type présent dans le référentiel ne sera sans doute plus adapté à nos besoins.</p>

<p style="text-align: justify;">Mais que faire ? Laisser tomber les jointures et stocker vos données sous formes d'agrégats dans des bases orientées documents ? Pourquoi ne pas plutôt repenser nos données et les appréhender sous un autre angle .</p>

<h2 style="text-align: justify;">Au delà du stockage</h2>

<p style="text-align: justify;">Lorsque j'ai acheté l'excellent livre <a href="http://graphdatabases.com/">graphDatabase,</a> je m'attendais à une lecture très technique concernant du <strong>stockage de données</strong>. Les auteurs (tous 3 créateurs de <a href="http://www.neo4j.org/">neo4J</a>) ont une vision très axée sur le <strong>domaine métier</strong>, toujours en quête d'apporter une valeur métier aux données.</p>

<p style="text-align: justify;">Il faut d'ailleurs savoir que toutes les graphDatabases n'utilisent pas un stockage natif graphe. Certaines utilisent des bases de données relationnelles ou noSql orientée document pour stocker les nœud et relations.</p>

<p style="text-align: justify;">Il s'agit donc avant tout d'une façon de penser, et non d'une simple amélioration technique.</p>

<h3 style="text-align: justify;">Transformez la complexité accidentelle en valeur</h3>

<p style="text-align: justify;">Dans un modèle de type e-commerce simple, avec des utilisateurs passant des commandes et notant des produits, on pourrait se retrouver avec un schéma tel que celui ci:</p>

<p style="text-align: justify;"><img alt="" src="http://www.arolla.fr/blog/wp-content/uploads/2013/09/modele_commande.png" /></p>

<p style="text-align: justify;">Les relations représentées par des clés étrangères alourdissent le modèle sans y apporter de valeur et permettent juste de lier vos données techniquement.</p>

<p style="text-align: justify;"><span style="line-height: 1.6em;">L'ajout de clefs étrangères liant deux tables et de données qualifiant ce lien comme sa date de création, son créateur, vont alourdir vos tables. Lorsque certaines tables sont centrales et sont de plus en plus utilisées au fil du temps par différentes fonctionnalités, ces liens qualifiés deviennent lourds à maintenir. Cela va à terme créer des</span><span style="line-height: 1.6em;"> zones de friction entre vos différents domaines et polluer votre modèle.</span></p>

<p style="text-align: justify;">De plus on se retrouve rapidement avec des risques de requêtes coûteuses, voire récursives, si l'on souhaite exploiter ce modèle.</p>

<p style="text-align: justify;">En base de données orientée graphe, les relations sont des éléments de premier niveau au même titre que les nœuds et peuvent ainsi apporter une vraie valeur, et cela permet à n'importe qui arrivant hors contexte de mieux comprendre la nature de la connexion entre ces 2 objets.</p>

<p style="text-align: justify;"><img alt="graph example" src="http://www.arolla.fr/blog/wp-content/uploads/2013/09/graph-ecommerce.png" /></p>

<p style="text-align: justify;">L'optimisation apportée par la recherche locale désacralise la séparation des données. La facilité à retrouver les données connectées rend plus facile le découpage en petits objets, afin de ne pas mélanger les domaines métier.</p>

<p style="text-align: justify;">Une fois le premier nœud trouvé, récupérer ses voisins (données directement reliées fonctionnellement) prendra toujours le même temps, indépendamment de la volumétrie.</p>

<h3 style="text-align: justify;">The silver bullet ?</h3>

<p style="text-align: justify;">Bien entendu non, comme toute technologie celle-ci a ses limites et contre-indications:</p>

<ul style="text-align: justify;">
    <li>Traverser l'ensemble du graphe se révélera être coûteux : prix moyen d'une commande tous clients confondus par exemple (en opposition à une base relationnelle) ou toute autre valeur statistique globale telle que le chiffre d'affaires.</li>
    <li>Les recherches ne commençant pas par une entité identifiée sont contre-indiquées.</li>
    <li>Changer son modèle nécessitera de repasser sur tous ses objets manuellement, c'est le 2e effet kiss cool du <strong>schéma-less</strong>. L’intérêt étant aussi d'éviter une migration de schéma en cas d'ajout d'une nouvelle propriété.</li>
    <li>Sur une reprise d'existant, un schéma permet d'avoir une vue d'ensemble. Avec une base de données orientée graphe, on ne pourra se baser sur un seul nœud pour connaitre les cardinalités et attributs potentiels, mais regarder l'ensemble des données (ou se baser sur le code directement)</li>
</ul>

<p style="text-align: justify;">Cependant c'est la solution incontestables lorsque :</p>

<ul style="text-align: justify;">
    <li>vous auriez besoin de requêtes récursives sur une table relationnelle, cette notion de récursivité n'existe pas en graphe database car les objets ne sont pas stockés dans des tables structurées.</li>
    <li>vos recherches partent d'un objet précis et retournent les objets qui lui sont liés.</li>
    <li>vous souhaitez faire des recherches par motifs : (A)=like=&gt;(B)&lt;=like=(C) pour trouver les gens qui ont les même centres d’intérêt par exemple.</li>
    <li>vous avez atteint un cap avancé de dé-normalisation.</li>
</ul>

<p style="text-align: justify;">Entre deux il reste toute une gamme de cas d'utilisation où vous pouvez utiliser indifféremment l'une ou l'autre solution.  Et gardez à l'esprit que nous sommes à l'ère de la Polyglot Persistence et qu'il ne faut pas forcement se restreindre à une seule solution de stockage.</p>

<p style="text-align: justify;">A savoir tout de même que la version 2 de neo4J permettra l'utilisation de "labels" afin de pouvoir typer ces nœuds, et d'y définir des contraintes d'unicité sur certains champs (email dans le label Person par exemple), permettant ainsi à ceux et celles le désirant de retrouver un début de schéma.</p>

<h3 style="text-align: justify;"></h3>

<h3 style="text-align: justify;"><span>A vous de jouer</span></h3>

<h3 style="text-align: justify;"><span style="font-size: 14px; line-height: 1.6em;">Si le sujet vous intéresse, vous pouvez maintenant</span><span style="font-size: 14px; line-height: 1.6em;"> </span><strong style="font-size: 14px; line-height: 1.6em;">écouter </strong>l'épisode des CastCodeurs dédié à neo4J<span style="font-size: 14px; line-height: 1.6em;">, <strong>regarder</strong> cette <a href="http://www.infoq.com/presentations/graph-database-theory">vidéo sur la théorie des graphes</a>, ou encore </span><strong style="font-size: 14px; line-height: 1.6em;">lire </strong><span style="font-size: 14px; line-height: 1.6em;">l'excellent livre </span><a style="font-size: 14px; line-height: 1.6em;" href="http://graphdatabases.com/">graphDatabase</a><span style="font-size: 14px; line-height: 1.6em;"> dont la première partie a très largement inspiré cet article.</span></h3>
