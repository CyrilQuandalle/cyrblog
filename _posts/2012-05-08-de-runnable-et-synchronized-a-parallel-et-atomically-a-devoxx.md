---
layout: post
title: De Runnable et synchronized à parallel() et atomically() à Devoxx
date: 2012-05-08 22:13
author: nouhoum traore
comments: true
categories: [Concurrence, devoxxFR, Evénements, jvm, oui, Programmation, Programmation]
---
<p style="text-align: justify;" dir="ltr"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/04/Devoxx2012.png"><img class="aligncenter size-medium wp-image-535" title="Devoxx2012" src="http://www.arolla.fr/blog/wp-content/uploads/2012/04/Devoxx2012-300x162.png" alt="" width="300" height="162" /></a></p>

<p style="text-align: justify;" dir="ltr">Le jeudi 18 avril, c’est le premier jour de la première édition de Devoxx France qui démarre (Devoxx sous la tour Eiffel) ! C'est historique pour la communauté Java de France. Et j'y suis avec d'autres consultants Arolla. Le fait d'avoir plusieurs talks intéressants sur le même créneau horaire rend le choix parfois difficile.</p>

<p style="text-align: justify;" dir="ltr">Je commence par une université (session de 3 heures) de José Paumard, maître de conférence à Paris 13, indépendant et vieux routard de la programmation. Le sujet de la conférence est la programmation concurrente sur la JVM. C’est très dense et je vais essayer d'en retracer ici les grandes lignes.</p>

<p style="text-align: justify;" dir="ltr">Comment tirer parti de la puissance des processeurs ? Quels sont les impacts des processeurs multi-cœurs sur notre façon d'écrire du code ? Quels challenges nous pose le développement d'applications parallèles ? Voici quelques questions auxquelles le talk José a apporté des éléments de réponse.</p>

<p style="text-align: justify;" dir="ltr"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/05/de_runnable_a_parallel_jose_paumard.jpg"><img class="alignnone size-medium wp-image-546 aligncenter" src="http://www.arolla.fr/blog/wp-content/uploads/2012/05/de_runnable_a_parallel_jose_paumard-300x225.jpg" alt="" width="300" height="225" /></a></p>

<h1 style="text-align: justify;"></h1>

<h2 style="text-align: justify;">La concurrence sur la JVM aux premières heures de Java</h2>

<p style="text-align: justify;" dir="ltr">A ses débuts en 1995 (JDK 1), l'API proposée par Java pour faire de la concurrence était assez peu élaborée. Elle comportait les éléments suivants entre autres : l'interface <em>Runnable</em>, les mots-clés <em>synchronize</em> et <em>volatile</em> et la classe <em>Thread</em>. Chaque objet disposait d'un moniteur. Les problématiques de multithreading qui prévalaient à cette époque sont différentes de celles de nos jours. Les processeurs étaient pour la plupart mono-coeurs et le multithreading consistait au découpage en slots du temps de calcul du processeur. Ce qui signifie qu'à un moment donné, seul un thread s'exécute puis il est mis en attente, un autre s'exécute ainsi de suite. Les causes pour lesquelles un thread peut être mis en attente sont diverses. Par exemple il est en attente de la disponibilité d’un autre thread, de la fin d’un traitement effectué par un autre ou afin de permettre à un autre d’utiliser le temps de la CPU. Que se passait-t-il quand les « performances » des applications n’étaient pas à la hauteur ? La réponse était... “acheter une machine avec un processeur plus puissant”. L'augmentation continue de la fréquence des processeurs rendait cela possible (cf. la <a title="La loi de Moore" href="http://fr.wikipedia.org/wiki/Loi_de_Moore">loi de Moore</a>). Cette réalité serait la cause du peu de changement de l'API de concurrence en Java jusqu'en 2004.</p>

<h2 style="text-align: justify;">La stagnation de l’augmentation des fréquences et l'arrivée des processeurs multi-coeurs</h2>

<p style="text-align: justify;" dir="ltr">Mais... les choses ont commencé à "se gâter" avec la stagnation de la fréquence des microprocesseurs. Ce fût la fin de «la soupe gratuite ». Désormais, l’augmentation de la puissance de calcul passe par la <a href="http://fr.wikipedia.org/wiki/Microprocesseur_multi-c%C5%93ur">multiplication du nombre de coeurs</a> ou l’usage du processeur graphique (<a href="http://en.wikipedia.org/wiki/GPGPU">GPGPU</a>). Aujourd’hui, il existe des processeurs grand public possédant jusqu’à 8 coeurs tandis que certains processeurs professionnels ont jusqu’à 128 coeurs. Cette nouvelle donne change la façon dont nous écrivons des programmes. Java 5 met à disposition du développeur des API de concurrence plus avancées, et groupées dans le package <em>java.util.concurrent</em>. Mais, avant d’explorer en détails ces API, José nous parle de certains problèmes liés au multi-threading.</p>

<h2 style="text-align: justify;">Quelques problèmes typiques du multi-threading</h2>

<p style="text-align: justify;" dir="ltr">Les problèmes liés au multi-threading abordés lors de la présentation de José :</p>

<ul style="text-align: justify;">
    <li>
<p dir="ltr"><strong>Les data races</strong> : Une “<a href="http://en.wikipedia.org/wiki/Race_condition">data race</a>” arrive lorsque deux processus qui s’exécutent en même temps produisent un résultat différent selon leur entrelacement. Dans ce genre de situation, si on ne fait pas attention, les choses peuvent très mal se passer. L’exemple du <a href="http://fr.wikipedia.org/wiki/Singleton_%28patron_de_conception%29">Singleton</a> a été utilisé pour illustrer le propos. On peut par exemple se retrouver avec deux instances d’une classe qui est censée n'en avoir qu’une seule. Le présentateur a écrit <a href="http://blog.paumard.org/2011/04/22/bilan-sur-le-pattern-singleton/">un post</a> très détaillé sur le sujet sur son blog. En résumé les solutions aux “data races” sont la synchronisation (mot-clé <em>synchronize</em>) ou l’immutabilité des variables.</p>
</li>
    <li>
<p dir="ltr"><strong>La visibilité des modifications</strong> : Les processeurs utilisent des caches (registres) pour stocker les données sur lesquelles ils sont en train d’effectuer des opérations, améliorant ainsi les performances. Mais cela peut avoir des conséquences dans un programme en multi-thread. Prenons par exemple le cas d’une donnée partagée par deux threads qui s’exécutent sur deux coeurs. Chaque coeur peut copier la donnée dans son cache local. Ainsi, les modifications effectuées par chacun sur sa version locale de la donnée peuvent ne pas être visibles immédiatement de l’autre. Les mots-clés <em>volatile</em> et <em>synchronize</em> apportent la solution à ce problème. En effet, une variable annotée avec <em>volatile</em> n’est pas cachée par le processeur et toute lecture de la variable renvoie à la dernière valeur écrite dans la variable. De même, les modifications effectuées dans un bloc <em>synchronize</em> sont visibles à la sortie de ce bloc.</p>
</li>
    <li>
<p dir="ltr"><strong>Le deadlock</strong> : Un autre problème pouvant survenir en programmation concurrente est le <a href="http://fr.wikipedia.org/wiki/Interblocage">deadlock</a> ou l'“étreinte fatale” pour les plus poètes d’entre nous. Cela arrive lorsque deux threads s’attendent mutuellement. La conséquence est que chacun se retrouve bloqué. Le sujet du deadlock est assez compliqué. Si vous souhaitez en savoir davantage, visualisez la conférence (intitulée "Deadlock Victim") d’Olivier Croisier et de Heinz Kabutz sur <a title="Parleys" href="https://www.youtube.com/user/Parleysdotcom">parleys.com</a>.</p>
</li>
</ul>

<p style="text-align: justify;" dir="ltr">A partir de tout ça, comment crée-t-on un thread ?</p>

<h2 style="text-align: justify;">Création et démarrage d’un thread</h2>

<p style="text-align: justify;" dir="ltr">La première chose à faire est de définir la tâche en implémentant l’interface <em>Runnable</em>.</p>

[java]
public class Task implements Runnable {
  public void run() {
    //Do something here.
  }
}
[/java]

<p style="text-align: justify;" dir="ltr">Ensuite, on crée un objet de type <em>Thread</em> en lui passant une instance de la classe <em>Task</em> puis, on le lance en invoquant la méthode <em>Thread#start()</em>.</p>

[java]
Thread thread = new Thread(new Task());
thread.start();
[/java]

<p style="text-align: justify;">Il y a mieux que la méthode <em>Thread#start()</em> pour lancer un thread : <em>java.util.concurrent.ExecutorService</em>.
L’<em>ExecutorService</em> s’adapte à la machine et s’occupe de la création des threads contrairement à la gestion manuelle par la méthode<em> Thread#start()</em>. Cela donne :</p>

[java]
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
....
ExecutorService executorService = Executors.newCachedThreadPool();
Task task = new Task();
executorService.submit(task);
[/java]

<p style="text-align: justify;"><em>java.util.concurrent.Executors</em> est une classe Factory qui met à disposition plusieurs méthodes pour créer des <em>ExecutorService</em> en fonction de votre besoin.
Jusque-là, nous avons défini les tâches avec l'interface <em>Runnable</em> qui dispose d’une unique méthode qui ne renvoie aucune valeur. Si vous souhaitez ne créer qu'une tâche qui renvoie une valeur, il existe une interface pour cela : <em>java.util.concurrent.Callable</em>.
Voici la définition de la tâche :</p>

[java]
import java.util.concurrent.Callable;

public class CallableTask implements Callable&amp;amp;amp;lt;Integer&amp;amp;amp;gt; {
  public Integer call() throws Exception {
    int result = 0;
    // Do something hard and store the value in result variable.
    return result;
  }
}
[/java]

<p style="text-align: justify;">Pour la lancer, on se sert comme précédemment de l’<em>ExecutorService</em> :</p>

[java]
import java.util.concurrent.Future;
...
Future&amp;amp;amp;lt;Integer&amp;amp;amp;gt; resultFuture = executorService.submit(new CallableTask());
[/java]

<p style="text-align: justify;">Lorsque l’on soumet à l’<em>ExecutorService</em> une tâche de type <em>Callable, </em>nous obtenons en retour un objet de type <em>Future</em>. C’est un objet qui porte bien son nom car il contiendra le futur résultat qui sera obtenu à l’issue de l’exécution de la tâche. La méthode bloquante <em>Future#get</em> permet de récupérer le résultat :</p>

[java]
int result = resultFuture.get();
[/java]

<p style="text-align: justify;">Vous savez désormais créer et lancer un thread ! Il est temps d’aborder les autres APIs que José a présentées.</p>

<h2 style="text-align: justify;">Quelques APIs de concurrence de Java</h2>

<p style="text-align: justify;" dir="ltr">Voyons quelques classes de concurrence disponibles en Java depuis la version 5 :</p>

<ul style="text-align: justify;">
    <li><em><strong>Lock</strong></em> : Un lock permet de contrôler l’accès à une ressource partagée par plusieurs threads. L’interface commune aux différentes implémentations est  <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/locks/Lock.html">Lock</a>. Ce type de verrou permet plus de contrôle que le verrou intrinsèque disponible sur tout objet Java.</li>
    <li>
<p dir="ltr"><em><strong>Semaphore</strong></em> : Un <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/Semaphore.html">sémaphore</a> est un objet qui permet de contrôler l’accès à un nombre N de ressources.</p>
</li>
    <li>
<p dir="ltr"><em><strong>Barrier</strong></em> : Encore une autre classe intéressante et pratique dans certaines situations : <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/CyclicBarrier.html">CyclicBarrier</a>. Un objet de ce type permet à un ensemble de threads de se rencontrer à un point donné comme par exemple à la fin de plusieurs traitements. Il est possible d’implémenter la même fonctionnalité avec un <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/CountDownLatch.html">CountDownLatch</a>.</p>
</li>
    <li>
<p dir="ltr"><em><strong>Atomic</strong></em>(Long, Float, …) : Les classes groupées dans le package <em>java.util.concurrent.atomic</em> permettent de faire des opérations atomiques basées sur l’instruction “<a href="http://fr.wikipedia.org/wiki/Compare-and-swap">compare and swap</a>”. Je vous invite à explorer <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/atomic/package-summary.html">ce package</a> pour en savoir davantage.</p>
</li>
    <li>
<p dir="ltr"><em><strong>BlockingQueue</strong></em> :  L’interface <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/BlockingQueue.html">BlockingQueue</a> simplifie grandement l’application du pattern consommateur/producteur (cf. la javadoc pour un exemple simple).</p>
</li>
    <li>
<p dir="ltr"><em><strong>CopyOnWriteArrayList</strong></em> : Une autre structure de données astucieuse introduite en <a href="http://docs.oracle.com/javase/1.5.0/docs/api/java/util/concurrent/CopyOnWriteArrayList.html">Java 5</a>. L’idée est la suivante : les lectures ne sont pas synchronisées contrairement aux opérations qui modifient la collection. Pour ces dernières, une copie du tableau sous-jacent est faite. Ce qui a pour conséquence de rendre l’usage d’une CopyOnWriteArrayList coûteux dans les cas où les modifications sont nombreuses.</p>
</li>
</ul>

<h2 style="text-align: justify;">Les alternatives à la synchronisation</h2>

<p style="text-align: justify;" dir="ltr">La deuxième partie de la conférence était consacrée aux alternatives à la concurrence basée sur la synchronisation (<em>synchronize</em>).  Commençons par la mémoire transactionnelle logicielle plus connue sous le doux nom de <a href="http://fr.wikipedia.org/wiki/M%C3%A9moire_transactionnelle_logicielle">STM</a> (Software Transactional Memory).</p>

<p style="text-align: justify;" dir="ltr"><a href="http://www.arolla.fr/blog/wp-content/uploads/2012/05/SAM_1515.jpg"><img class="aligncenter size-medium wp-image-556" src="http://www.arolla.fr/blog/wp-content/uploads/2012/05/SAM_1515-300x225.jpg" alt="" width="300" height="225" /></a></p>

<ul style="text-align: justify;">
    <li>
<p dir="ltr"><strong>La STM</strong> : La STM se base sur la notion de transaction comme on en a avec les bases de données pour gérer l’accès concurrent à des ressources partagées. La donnée partagée est encapsulée dans une référence. Les références sont modifiées dans une transaction. Deux scénarios possibles :</p>
</li>
</ul>

<ol style="text-align: justify;">
    <li>
<blockquote>Si la transaction échoue (à cause d’une collision par exemple), elle est relancée.</blockquote>
</li>
    <li>
<blockquote>Si la modification est réussie, la transaction est validée.</blockquote>
</li>
</ol>

<p style="text-align: justify;" dir="ltr">Parmi les points séduisants de la STM, on peut citer la simplicité de la syntaxe et la facilité de raisonner sur le code ainsi que la composabilité des opérations (ce qui fait défaut aux verrous). D'après le présentateur, le bon cas applicatif de la STM est quand le niveau de concurrence n’est pas trop élevé. En effet, s’il y a trop de concurrence, les collisions sont nombreuses et dégradent ainsi les performances. Le projet <a href="https://dzone.com/articles/multiverse-open-source">Multiverse</a> permet de faire de la STM en Java et pour ceux qui font du Scala, il y a <a href="http://nbronson.github.com/scala-stm/index.html">Scala STM</a>.</p>

<ul style="text-align: justify;">
    <li>
<p dir="ltr"><strong>Les acteurs</strong> : Le modèle des acteurs est une autre alternative aux verrous pour écrire des programmes concurrents. L’article fondateur du modèle date de 1973. Un acteur est un objet qui dispose d’une boîte de réception, y reçoit des messages (immutables), réalise un traitement et peut potentiellement transmettre un message en réponse au monde extérieur. Un aspect important du modèle des acteurs est qu’il ne partage pas son état avec le monde extérieur. Si vous souhaitez en savoir davantage sur les acteurs, vous pouvez aller du côté d’<a href="http://akka.io/">Akka</a> qui est un excellent outil. Pour info, l'API d'Akka existe à la fois en Scala et en Java.</p>
</li>
</ul>

<h2 style="text-align: justify;">Le reste en vrac</h2>

<p style="text-align: justify;" dir="ltr">Dans la dernière partie de la conférence, José a parlé de la parallélisation des calculs, de <a title="Fork/Join" href="http://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html">Fork/Join</a> et de l’importance de l’algorithmique.</p>

<h2 style="text-align: justify;">Conclusion</h2>

<p style="text-align: justify;" dir="ltr">Cette présentation était très dense. Le sujet est à la fois riche et complexe. Le présentateur, bon orateur, avait une bonne maîtrise du sujet. Pensez à faire un tour sur <a title="Parleys" href="https://www.youtube.com/user/Parleysdotcom">parleys.com</a> afin de visualiser la vidéo. Cela vaut vraiment le coup !</p>
