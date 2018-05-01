---
layout: post
title: Les bons tuyaux de core.async
date: 2014-08-04 10:26
author: jerome prudent
comments: true
categories: [acteur, clojure, core.async, Fonctionnel, frp, IoC, Programmation]
---
<h1>Les bons tuyaux de core.async</h1>

<a href="http://clojure.org">Clojure</a> est le langage le plus simple et le plus cohérent que je connaisse. Le cœur du langage est très concis et fournit pourtant tous les outils nécessaires à l'écriture efficace de programmes modernes : manipulation de données et multithreading.

<a href="https://github.com/clojure/core.async">core.async</a> est une librairie qui introduit des outils très simples conceptuellement qui offrent des possibilités incroyables. Elle fait partie du projet clojure mais est facultative.
<code>core.async</code> est similaire à ce que propose <a href="http://golang.org">Go</a> avec ses <a href="http://www.golang-book.com/10/index.htm">goroutines et channels</a>. Ces concepts étant eux-mêmes inspirés de l'algèbre <a href="http://fr.wikipedia.org/wiki/Communicating_sequential_processes">CSP</a>.

L'objectif de cet article est de vous montrer quelques possibilités offertes par <code>core.async</code> en introduisant petit à petit les concepts. Nous verrons comment <code>core.async</code> permet de découpler son code en producteur / consommateur d'information, comment elle permet d'encapsuler un traitement dans une unité de travail, comment elle permet de paralléliser son code sans contrainte, et comment elle ouvre les portes à la programmation réactive.

<h4>Exemples</h4>

La plupart des exemples tournent autours du <a href="http://en.wikipedia.org/wiki/Morra_%28game%29">jeu de Morra</a> qui est une espèce de shi-fu-mi antique. Deux joueurs doivent en même temps produire un nombre avec leurs doigts et crier la somme des doigts des deux joueurs. Le gagnant est celui qui a bien deviné la somme.

Pour taper vous-même les exemples dans un repl, les imports suivants sont suffisants :

[code lang=clojure]
(require &#039;[ clojure.core.async :as async :refer [thread chan &lt;!! &lt;! &gt;!! &gt;! go go-loop]])
[/code]

<h4>Channels</h4>

L'élément central de core.async est le <em>channel</em>.
Il s'agit d'un tuyau, d'un canal, conceptuellement proche d'une <em>queue</em> dans un outils de messaging.

Un <em>channel</em> se crée simplement :

[code lang=clojure]
(chan)
[/code]

Un tel <em>channel</em> ne peut contenir qu'une seule valeur. Cela signifie que l'écriture dans ce <em>channel</em> est bloquée tant que personne ne lit la valeur présente.

Un <em>channel</em> peut être <em>bufferisé</em> pour contenir plusieurs valeurs. Ici on crée un <em>channel</em> qui peut contenir 2 valeurs :

[code lang=clojure]
(chan 2)
[/code]

Un tel <em>channel</em> permet d'écrire consécutivement 2 valeurs sans être obligé d'attendre que quelqu'un ne lise les valeurs.

Je conseille de travailler avec des <em>channels</em> <em>non bufferisés</em> dans un premier temps. Cela rend le raisonnement et la compréhension du comportement plus aisés.

Un <em>channel</em> est une valeur comme une autre. Ici on l'associe à une <a href="http://clojure.org/vars">Var</a> :

[code lang=clojure]
(def player1 (chan 1))
[/code]

On pourrait donc associer le <em>channel</em> <code>player1</code> dans une <code>Map</code> en tant que clé ou valeur par exemple. Et si on est vicieux, on peut même le faire transiter dans un autre <em>channel</em>.

<h2>Inversion Of Control</h2>

L'<a href="http://martinfowler.com/bliki/InversionOfControl.html">IoC</a>, est un concept assez général dont le but est de casser les dépendances qu'un module a avec un autre. L'<a href="http://fr.wikipedia.org/wiki/Injection_de_d%C3%A9pendances">injection de dépendance</a> permet de faire de l'IOC (voir <a href="https://github.com/google/guice/wiki/GettingStarted">Guice</a>, <a href="http://projects.spring.io/spring-framework/">Spring</a>, ...). Mais la programmation événementielle, dans le navigateur, où l'on <em>bind</em> un événement à une <em>callback</em> est aussi une forme d'IOC, dans le sens où le flot d'exécution n'est plus programmé séquentiellement. Quand je dis que <code>core.async</code> permet de faire de l'IOC, c'est plus dans ce sens.

<h3>Traditionnellement</h3>

Dans les langages fonctionnels, l'IoC par <a href="">injection de dépendance</a> est assez naturelle puisqu'on peut passer des fonctions en paramètre (fonctions de 1er ordre) :

[code lang=clojure]
(defn print-claim [m] (print m))

(defn player-claim [fingers sum handler]
  (handler [fingers sum]))

(player-claim 5 8 print-claim)
[/code]

Ceci affiche <code>5 8</code> dans la console. Le comportement de la fonction <code>player-claim</code> a été injecté.

<h3>Avec core.async</h3>

Avec <code>core.async</code>, on part d'un <em>channel</em> non bufferisé :

[code lang=clojure]
(def player1 (chan))
[/code]

Pour écrire dans un channel, on utilise la fonction bloquante <code>&gt;!!</code>

[code lang=clojure]
(&gt;!! player1 [5 8])
[/code]

A ce stade, <code>player1</code> contient le vecteur <code>[5 8]</code>. Par contre l'exécution du programme est bloquée jusqu'à ce que la valeur du <em>channel</em> soit lue. Il existe bien une opération de lecture <code>&lt;!!</code>, mais puisqu'on est bloqué, on ne pourra jamais l'exécuter dans le même thread. Comment faire ?

La solution est d'effectuer l'opération de lecture dans une autre unité d'exécution, soit un thread.

[code lang=clojure]
(thread
  (loop [m &quot;player 1 moves : &quot;]
    (println m)
    (recur (&lt;!! player1))))
[/code]

La macro <code>thread</code> permet de créer ... un thread. Ce thread boucle indéfiniment. A chaque itération de boucle il affiche la valeur
contenue dans le <em>channel</em> <code>player1</code>. Et dans un REPL ça donne :

[code lang=clojure]
    user=&gt; (def player1 (chan))
    #&#039;user/player1

    user=&gt; (thread (loop [m &quot;player 1 moves : &quot;] (println m) (recur (&lt;!! player1))))
    player 1 moves :
    #

    user=&gt; (&gt;!! player1 [5 8])
    true
    [5 8]

    user=&gt; (&gt;!! player1 [1 2])
    [1 2]
    true

    user=&gt; (&gt;!! player1 [1 3])
    [1 3]
    true
[/code]

On a un thread qui produit les mouvements du joueur 1 sans savoir ce qui en sera fait. Et on a un autre thread qui reçoit les mouvements et les affiche. Au runtime nous avons nous même fait l'association producteur / consommateur. Cela correspond à la notion d'<a href="http://martinfowler.com/bliki/InversionOfControl.html">Invertion of Control</a>, si ce n'est qu'on a codé notre propre framework et utilisant un <em>channel</em> pour faire la colle entre les composants.

La version du code utilisant <code>core.async</code> va plus loin en rendant le code asynchrone. A chaque composant sa spécialisation, à chaque composant son unité d'exécution.

<h2>Modèle d'acteur</h2>

<a href="http://fr.wikipedia.org/wiki/Mod%C3%A8le_d%27acteur">Wikipedia</a> définit le modèle d'acteur ainsi :

<blockquote>
  Un acteur est une entité capable de calculer, qui, en réponse à un message reçu, peut parallèlement :
  
  1) envoyer un nombre fini de messages à d’autres acteurs ;
  
  2) créer un nombre fini de nouveaux acteurs ;
  
  3) spécifier le comportement à avoir lors de la prochaine réception de messages.
</blockquote>

Etudions ce petit programme :

[code lang=clojure]
(def player1 (chan))
(def player2 (chan))
(def result (chan))

(thread
  (loop [scores {:player1 0 :player2 0 :draw 0}]
   (println &quot;Scores : &quot; scores)
   (recur
    (update-in scores [(&lt;!! result)] inc))))

(defn winner [correct-guess p1-guess p2-guess]
  (cond
   (= p1-guess p2-guess) :draw
   (= correct-guess p1-guess) :player1
   (= correct-guess p2-guess) :player2
   :else :draw))

(thread
 (loop []
   (let [[p1-fingers p1-guess] (&lt;!! player1)
         [p2-fingers p2-guess] (&lt;!! player2)
          correct-guess (+ p1-fingers p2-fingers)]
     (&gt;!! result (winner correct-guess p1-guess p2-guess))
     (recur))))
[/code]

On a 2 unités d'exécution. La première est dédiée à l'affichage du score. A chaque fois qu'une valeur est présente dans <code>result</code> la valeur de <code>scores</code> est mise à jour.

Notons que la valeur actuelle du score est complètement <em>encapsulée</em>. Personne d'autre ne peut connaitre le score.
La valeur du score courant est dépendante de la valeur précédente. Le 3ème point de la définition d'un acteur, <em>spécifier le comportement à avoir lors de la prochaine réception de messages</em>, est bien vérifié.

La seconde unité d'exécution lit tour à tour dans les <em>channel</em> <code>player1</code> et <code>player2</code>, détermine un gagnant et écrit le résultat dans <code>result</code> qui sera relu par notre 1ère unité d'exécution. Le 1er point de la définition d'un acteur, <em>envoyer un nombre fini de messages à d’autres acteurs</em>, est vrai.

Concernant le 2ème point de la définition, <em>créer un nombre fini de nouveaux acteurs</em>, je n'ai pas trouvé quelque chose de pertinent pour le démontrer. Cela est néanmoins possible si on remplace le premier acteur par ceci :

[code lang=clojure]
(defn print-stats [stats]
  (let [total (apply + (vals stats))]
    (println &quot;wins : &quot; (:wins stats) &quot;/&quot; total)
    (println &quot;draws : &quot; (:draws stats) &quot;/&quot; total)))

(defn run-stats [channel]
  (loop [stats {:wins 0, :draws 0}
         total 0]
      (print-stats stats)
      (recur
       (update-in stats [(&lt;!! channel)] inc)
       (inc total))))

(thread
  (let [stats (chan)]
    (thread (run-stats stats))
      (loop [scores {:player1 0 :player2 0 :draw 0}]
        (println &quot;Scores : &quot; scores)
        (let [winner (&lt;!! result)]
        (&gt;!! stats (if (= :draw winner) :draw :wins))
        (recur (update-in scores [winner] inc))))))
[/code]

La présence des deux threads imbriqués indique le lancement d'un acteur par un autre. Le 2ème point de la définition est vérifié.

Je pense qu'on peut appeler chaque thread un acteur, car on vient de voir que cela répond à la définition. Certes ce sont des acteurs très basiques (pas de pattern matching, pas de stratégie de mailbox, pas de priorité de messages, ...).
Au pire on peut dire que ce sont des objets, dans le sens où ils encapsulent un état et communiquent par message.

<h2>Programmation concurrente illimitée</h2>

Dans les précédents exemples nous avons instancié des threads pour consommer les valeurs contenues dans les <em>channels</em>. Chacun de ces threads consistait en une boucle infinie qui bloquait sont exécution jusqu'à ce qu'une valeur soit lisible. Ce modèle est limité par les <a href="http://stackoverflow.com/questions/763579/how-many-threads-can-a-java-vm-support">quelques milliers</a> de threads que l'on peut lancer sur la VM. Sans compter <a href="http://blog.tsunanet.net/2010/11/how-long-does-it-take-to-make-context.html">le temps perdu</a> en <a href="http://en.wikipedia.org/wiki/Context_switch">context switch</a>.

Imaginons maintenant que notre plateforme de jeu n'ait pas quelques milliers de threads mais plutôt un centaine de milliers.

[code lang=clojure]
(dotimes [t 100000]
  (let [c language=&quot;(chan)&quot;][/c]

(thread
(loop []
(let [[_ v :as tv] (&lt;!! c)]
(if (= 0 (mod v 100)) (println tv)))
(recur)))

(thread
(doseq [v (iterate inc 1)]
(&gt;!! c [t v])))))
[/code]

Ici on crée 2 threads 100000 fois. Le premier lit dans un channel et affiche le résultat si c'est un multiple de 100. Le second écrit les entiers de 1 à l'infini.

10000 lignes environ s'affichent puis plus rien. Le CPU monte à 100%, la JVM compte 32000 threads et boom, IOException.

Pour pallier ces limitations, <em>core.async</em> offre la possibilité de créer des processus ultra légers, capables de se partager un pool de threads et qui peuvent se mettre en pause sans bloquer le thread d'exécution.

[code lang=clojure]
(dotimes [t 100000]
(let

[c language=&quot;(chan)&quot;][/c]


(go
(loop []
(let [[t v :as tv] (&lt;! c)]
(if (= 0 (mod v 100)) (println tv)))
(recur)))
(go
(doseq [v (iterate inc 1)]
(&gt;! c [t v])))))
[/code]

Dans cette version du programme, j'ai remplacé les threads créés par <code>thread</code> par des processus ultra légers via <code>go</code> et les appels bloquants <code>&amp;gt;!!</code> et <code>&amp;lt;!!</code> par leurs versions non-bloquantes <code>&amp;gt;!</code> et <code>&amp;lt;!</code>.

Le programme est stable et régulier : des salves d'informations s'affichent sur la console toutes les 40 secondes environ, les 8 cœurs de mon CPU sont à 40%, la consommation du heap oscille entre 1700 mégas et 160 après GC, et la JVM compte 70 threads.

Nous avons donc réussi à <em>simuler</em> 200000 threads ! J'ai oublié de préciser que <em>ce code peut aussi tourner sur un navigateur</em> ... Oui on peut écrire du code asynchrone dans le navigateur sans callbacks (hell).

<h2>Functional Reactive Programming</h2>

Dans un article <a href="http://www.arolla.fr/blog/2014/05/experimentation-de-frp-avec-bacon-js/">précédent</a>, j'avais fait une micro présentation de FRP. Pour rappel, il s'agit d'un style de programmation basé sur la manipulation de flux de valeurs, à base de <code>map</code>, <code>filter</code>, <code>zip</code>, <code>concat</code>, ... En bout de chaîne de chaque flux, on peut "consommer" les valeurs du flux pour réaliser des effets de bord, un affichage ou une écriture en base par exemple.

Etudions ce bout de code :

[code lang=clojure]
(def player1 (chan))
(def player2 (chan))

(defn winner [correct-guess p1-guess p2-guess]
(cond
(= p1-guess p2-guess) :draw
(= correct-guess p1-guess) :player1
(= correct-guess p2-guess) :player2
:else :draw))

(defn process-round [[p1-fingers p1-guess] [p2-fingers p2-guess]]
(winner (+ p1-fingers p2-fingers) p1-guess p2-guess))

(def ch-results (async/map process-round [player1 player2]))
[/code]

On commence par définir les deux <em>channels</em> qui contiennent les inputs des deux joueurs.
Ensuite, on a la fonction <code>winner</code> que nous connaissons déjà. Puis vient la fonction <code>process-round</code> qui prend les inputs des 2 joueurs et retourne le gagnant.
Enfin, nous créons un nouveau <em>channel</em> référencé par <code>ch-results</code>. Cependant on le crée via la fonction <code>map</code>. <code>map</code> prend en paramètre un vecteur de <em>channels</em> et une fonction. La fonction a autant de paramètres que de <em>channels</em> dans le vecteur. <code>map</code> crée un <em>channel</em> qui contient le résultat de <code>f</code> appliqué aux premières valeurs des <em>channels</em> du vecteur.

Si on joue de la manière suivante :

[code lang=clojure]
(&gt;!! player1 [1 2])
(&gt;!! player2 [1 2])

(&gt;!! player1 [1 2])
(&gt;!! player1 [1 3])
[/code]

Alors <code>ch-results</code> contiendra <code>:draw</code>, <code>:player1</code>.

Poursuivons :

[code lang=clojure]
(defn async-reduce [f init ch-input]
(let [ch-output (chan)]
(go-loop [acc init]
(&gt;! ch-output acc)
(recur (f acc (&lt;! ch-input))))
ch-output))

(def ch-scores
(async-reduce #(update-in %1 [%2] inc) {:player1 0 :player2 0 :draw 0} ch-results))

(go (while true
(println &quot;Scores : &quot; (&lt;! ch-scores))))
[/code]

<code>async-reduce</code> est une fonction utilitaire qui est similaire à un <code>reduce</code> classique, sauf qu'au lieu de ne renvoyer qu'une seule valeur, elle renvoie un <em>channel</em> sur lequel elle publie tous les résultats intermédiaires.
Ensuite on crée un nouveau <em>channel</em> <code>ch-scores</code> via <code>async-reduce</code> qui contiendra tous les scores sucessifs, au fur et à mesure des parties.
Enfin un <code>go</code> block consomme <code>ch-scores</code> pour afficher les scores.

On a mergé, transformé des flux de données pour arriver au résultat attendu. Le side effect est à la fin du programme. Bref, du FRP.

Ce que j'aime avec ce style de programmation, c'est que c'est facilement extensible. Par exemple pour n'afficher que les résultats où un joueur a gagné, il suffit de modifier légèrement le flux de données avec un filtre :

[code lang=clojure]
(def ch-non-draw-results (async/filter&lt; #(not= :draw %) ch-results ))

(def ch-scores
(async-reduce #(update-in %1 [%2] inc) {:player1 0 :player2 0} ch-non-draw-results))
[/code]

Et une séquence de jeu ressemble à ça :

[code lang=clojure]
user=&gt; (&gt;!! player1 [1 2])
true
user=&gt; (&gt;!! player2 [1 2])
true

user=&gt; (&gt;!! player1 [1 2])
true
user=&gt; (&gt;!! player2 [1 3])
true
Scores : {:player2 0, :player1 1}

user=&gt; (&gt;!! player1 [1 2])
true
user=&gt; (&gt;!! player2 [1 3])
true
Scores : {:player2 0, :player1 2}
[/code]

<h2>Les mots de la fin</h2>

On a passé en revue 4 bénéfices que l'on peut tirer de <code>core.async</code> :
- Invertion Of Control
- Modèle d'acteur
- Programmation concurrente illimitée
- Functional Reactive Programming

On a réussi à implémenter tout ça avec une API super simple qui peut se résumer à :
- un tuyau
- des processus légers pour lire et écrire le tuyau

Comme nous l'avons vu, nous n'avons pas affaire à la lourdeur d'un framework qui impose une façon de faire, mais à une librairie apportant les briques nécessaires pour faire du <em>sur mesure</em>.

Cette librairie existe aussi en <a href="https://github.com/clojure/clojurescript">ClojureScript</a>. Cela vous permet d'écrire du code asynchrone sans callback dans le navigateur.

Bienvenue dans le futur.

<h2>Ressources</h2>

<a href="http://clojure.com/blog/2013/06/28/clojure-core-async-channels.html">Rationale</a> : explication des concepts fondamentaux et de leur origine.

<a href="http://clojure.github.io/core.async/">API</a>

<a href="https://github.com/clojure/core.async/blob/master/examples/walkthrough.clj">Walk throught</a> Des exemples à taper dans la REPL.

<a href="http://en.wikipedia.org/wiki/Morra_%28game%29">Le jeu de Morra</a> : le petit jeu implémenté dans cet article.
