---
layout: post
title: Le hashage cohérent
date: 2016-01-25 12:28
author: jerome prudent
comments: true
categories: [architecture, Bonnes pratiques de dév, craftsmanship, développement, p2p, Programmation, programming]
---
Le hashage cohérent a été introduit en 1997 par le papier [<a href="https://www.akamai.com/us/en/multimedia/documents/technical-publication/consistent-hashing-and-random-trees-distributed-caching-protocols-for-relieving-hot-spots-on-the-world-wide-web-technical-publication.pdf" target="_blank">Consistent Hashing and Random Trees: Distributed Caching Protocols for Relieving Hot Spots on the World Wide Web</a>] de Karger D., Lehman E., Leighton T., Panigrahy R., Levine M., Lewin, D.

Le hashage cohérent est aujourd'hui une brique fondatrice et incontournable à beaucoup de technologies dites distribuées. Comprendre ce qu'est le hashage cohérent permet d'appréhender ces technos et d'en imaginer le fonctionnement.

Ce billet a pour objectif de vulgariser le concept de hashage cohérent.

A la fin des années 90, les sites internet populaires commencent à crouler sous les requêtes des internautes. Chaque document servi est identifié par une requête unique, par exemple <code>http://www.altavista.fr/search?q=netscape</code>. Le gros serveur™ reçoit la requête, fait un calcul coûteux avant de servir un document à l'internaute. Et quand le serveur reçoit 15000 requêtes en même temps ... BOOM !!

On se rend compte que :

<ul>
<li>certaines url sont plus sollicitées que d'autres et qu'il est dommage de faire le même calcul plusieurs fois</li>
<li>le gros serveur est taillé pour les calculs et pas pour la distribution de contenu</li>
</ul>

La solution consiste à mettre un serveur de cache en frontal. Le serveur de cache a deux possibilités.
Soit il possède localement le résultat pour l'URL qu'on lui demande (en mémoire ou sur disque), dans ce cas il répond instantanément, soit il n'a pas le résultat alors il le demande au gros serveur, répond, et le stocke localement.
Avec ça, on a résolu le problème du calcul redondant et déporté la responsabilité de servir du contenu.
Mais quand le serveur de cache reçoit 30000 requêtes en même temps ... BOOM !!

Alors on multiplie les serveurs de cache et on ajoute un load balancer. Le load balancer reçoit toutes les URLs et les répartit aux serveurs de cache. Et voici, le schéma qu'il faut mettre en forme avec PowerPoint :

<img src="http://www.arolla.fr/blog/wp-content/uploads/2016/01/schema.jpe" alt="Image schéma complet" />

L'algorithme magique de répartition d'une URL à un serveur de cache est le suivant :

[code lang=javascript]
serveur-de-cache = hash(URL) modulo nb-de-serveur-de-cache
[/code]

Simplissime ! Pour une URL donnée, on sollicite toujours le même serveur de cache, et on sollicite le moins possible le gros serveur™. On a résolu pour de bon le problème de mise à l'échelle (scalabilité). On attend le prix Nobel.

Mais qu'est ce qui ce passe si on débranche l'un des serveurs de cache ? Et bien, <code>nombre-de-serveur-de-cache</code> est décrémenté, et l'algorithme magique ne distribue plus du tout les URLs aux mêmes serveurs de cache. Cela signifie que pendant un moment, les serveurs de cache sollicitent systématiquement le gros serveur™ et ... BOOM !!

Et c'est à ce moment que je commence à vous parler des algorithmes de hashage cohérents, et que je plagie [<a href="https://fr.wikipedia.org/wiki/Hachage_coh%C3%A9rent" target="_blank">Wikipedia</a>].
La façon la plus simple de saisir le concept est d'utiliser l'allégorie du cercle. Sur un cercle, on peut apposer n'importe quel serveur de cache par la formule magique :

[code lang=javascript]
angle-du-cercle = hash(serveur-de-cache) modulo 360
[/code]

On peut également apposer n'importe quelle URL par la formule magique :

[code lang=javascript]
angle-du-cercle = hash(URL) modulo 360
[/code]

Ce qui pourrait donner un cercle du genre :

<img src="http://www.arolla.fr/blog/wp-content/uploads/2016/01//cercle.jpe" alt="Image les serveurs et les URLs sur un cercle" />

Maintenant mettons-nous à la place du load-balancer. Comment trouver le serveur de cache responsable de l'URL <code>http://www.altavista.fr/search?q=netscape</code> ?

1) On calcule la position de l'URL sur le cercle, disons <code>73°</code>

2) Connaissant la position de tous les serveurs de cache sur le cercle, on recherche celui qui est le plus proche l'URL tout en étant plus loin selon le sens horaire, soit le serveur de cache n°2.

On a donc pour le moment les correspondances suivantes :

<ul>
<li>Serveur de cache n°1 : <code>http://www.altavista.fr/search?q=gopher</code></li>
<li>Serveur de cache n°2 : <code>http://www.altavista.fr/search?q=netscape</code></li>
<li>Serveur de cache n°3 : <code>http://www.altavista.fr/search?q=opera</code> et <code>http://www.altavista.fr/search?q=mosaic</code></li>
</ul>

Maintenant supprimons le serveur de cache n°1. La distribution des URLs est la suivante :

<ul>
<li>Serveur de cache n°2 : <code>http://www.altavista.fr/search?q=gopher</code> et <code>http://www.altavista.fr/search?q=netscape</code></li>
<li>Serveur de cache n°3 : <code>http://www.altavista.fr/search?q=opera</code> et <code>http://www.altavista.fr/search?q=mosaic</code></li>
</ul>

On constate que <strong>la distribution des URLs a très peu été bouleversée</strong>. Seules les URLs dont avait la charge le serveur de cache n°1 ont été redistribuées. Le gros serveur™ n'est sollicité que pour la clé <code>http://www.altavista.fr/search?q=gopher</code>.

Essayons de pousser encore le vice. Comment faire pour que lorsqu'un serveur de cache est supprimé, on ne doive pas solliciter du tout le gros serveur™ ?
Simplement en dupliquant les responsabilités. Par exemple, on peut décider de chercher le serveur de cache le plus proche dans le sens horaire OU dans le sens inverse. Ainsi l'URL <code>http://www.altavista.fr/search?q=netscape</code> peut être servie par le serveur de cache n°2 ou le serveur de cache n°1.

On peut aussi décider de répliquer N fois la responsabilité d'une URL, le load-balancer peut utiliser la formule magique :

[code lang=javascript]
angle-du-cercle = hash(URL + (random N)) modulo 360
[/code]

Une URL peut donc se retrouver à N positions différentes sur le cercle, et potentiellement être prise en charge par N serveurs de cache différents.

Avec un peu d'imagination, plein d'autres stratégies de réplication peuvent être implémentées.

On s’aperçoit que l'élément crucial de cet algorithme est la distribution des serveurs de ce cache sur le cercle. C'est la fonction de hashage qui en est responsable.
L'algorithme de hashage doit avoir une très bonne distribution. Notre exemple ne comporte que trois serveurs de cache, mais on peut remarquer la proximité géographique du serveur de cache n°1 et du serveur de cache n°2. Dans l'idéal, ces trois serveurs de cache se situent à une distance de 120° l'un de l'autre.

Il serait catastrophique d'avoir nos trois serveurs séparés de quelques degrés seulement. La charge serait très mal distribuée.

Si vous utilisez le langage Java, je vous déconseille d'utiliser la fonction <code>String.hashcode()</code> qui a une très mauvaise distribution car <code>"serveur2".hashCode() = "serveur1".hashCode() + 1</code>.

D'un autre côté, les fonctions de hashage cryptogragraphique, telles que <code>SHA1</code> ou <code>MD5</code>, bien qu'ayant une très bonne distribution, avec peu de collisions, sont coûteuses en CPU.

Je conseille d'utiliser des fonctions de hashages plus légères, telles que [<a href="https://en.wikipedia.org/wiki/MurmurHash" target="_blank">Murmur</a>], qui offre une bonne distribution, peu de collisions et est très rapide. Voir aussi cette [<a href="http://programmers.stackexchange.com/questions/49550/which-hashing-algorithm-is-best-for-uniqueness-and-speed/145633#145633" target="_blank">réponse sur stackexchange</a>] qui compare différentes fonctions de hashage non cryptographiques.

On ne peut pas se quitter sans essayer d'implémenter un hashage cohérent ! Voici donc quelques lignes de Clojure qui implémentent très simplement ce que j'ai décrit ci-dessus.

<script src="https://gist.github.com/jprudent/498ce9c18742f2ff836f.js"></script>

L'implémentation est en Clojure, je suis désolé si ça vous déboussole, mais c'est un langage qui m'est cher et que je ne peux m'empêcher d'utiliser quand je ne suis pas contraint à ne pas le faire.

Pour cette implémentation, j'ai abstrait un peu les concepts. Un serveur de cache est appelé <code>node</code>, les URLs sont juste de la <code>data</code>.

Tout d'abord on définit un protocole (en clojure un protocole est une espèce d'interface) appelé <code>ConsistentHashRing</code>.
Il y a trois fonctions. <code>add-node</code> nous permettrait d'ajouter un serveur de cache, <code>remove-node</code> d'en supprimer un et <code>find-node</code> de trouver un serveur de cache.

J'ai choisi d'implémenter le protocole sur une classe existante dans Clojure : <code>PersitentTreeMap</code>. Il s'agit d'une implémentation d'une hashmap qui permet d'itérer sur les clés dans leur ordre naturel. En Java il s'agirait de la classe <code>TreeMap</code>.

L'implémentation de <code>add-node</code> associe le hashCode de <code>node</code> à <code>node</code>. Contrairement à mes conseils, j'utilise <code>Object.hashCode()</code> comme fonction de hashage :(

L'implémentation de <code>remove-node</code> est triviale. Elle supprime la clé dont la valeur est le hashCode de <code>node</code>.

Enfin, <code>find-node</code> cherche le <code>node</code> le plus proche de la <code>data</code> que l'on cherche.

Cette implémentation est triviale, après tout elle ne fait que 13 lignes ! Mais je trouve qu'elle démontre bien la simplicité des concepts du hashing consistant.

Vous pouvez essayer d'implémenter les réplications par-dessus. Ou utiliser un autre langage ! Je serais ravi de faire une relecture de code ;)

Sortons maintenant de notre contexte initial, à savoir le load-balancer et les servers de cache. Que peut-on inventer grâce au hashage cohérent ? Allez, je vous aide un peu :

<ul>
<li>On peut faire un [<a href="https://en.wikipedia.org/wiki/GlusterFS" target="_blank">filesystem distribué</a>] tolérant aux pannes</li>
<li>On peut faire une [<a href="http://docs.hazelcast.org/docs/3.5/manual/html/datapartitioning.html" target="_blank">base de donnée mémoire distribuée</a>] (j'ai mis le lien qui explique le partitionnement des données, ça vous rappelle quelque chose ?)</li>
<li>On peut faire une [<a href="http://developer.couchbase.com/documentation/server/4.1/concepts/buckets-vbuckets.html" target="_blank">autre base de donnée mémoire distribuée</a>] (j'ai mis le lien qui explique ce qu'est un vBucket, ça vous rappelle quelque chose ?)</li>
<li>On peut faire [<a href="https://en.wikipedia.org/wiki/Kademlia" target="_blank">index distribué des fichiers qui sont partagés sur Emule</a>] (la notion de noeud le plus proche est toutefois plus compliquée !)</li>
</ul>

En fait, dès que vous entendez parler d'une technologie qui met les mots : distributed, autoscaling, resiliant, et replication dans
la même phrase, elle utilise sans doute un hashage cohérent sous le capot.

Edition n°1 : <code>s/consistant/cohérent/g</code>. Merci Franck.
Edition n°2 : Ce n'est pas <code>Object.hashcode()</code> mais <code>String.hashcode()</code> qui pose problème. Merci Rémi.
Edition n°3 : Coquille. Merci Marouane.
