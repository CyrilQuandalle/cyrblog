---
layout: post
title: "On en parle sur le web : tests d'UI, Management 3.0, LeSS, Gradle ..."
date: 2015-01-21 10:31
author: mathieu laurent
comments: true
categories: [Actu, revue de presse, Revues de presse]
---
<p style="text-align: justify;"><em><a href="https://twitter.com/echec_et_matt" target="_blank">La revue de presse de Mathieu</a></em></p>

<p style="text-align: justify;"><strong>AGILITY</strong></p>

<p style="text-align: justify;">Pour les personnes s’intéressant un peu aux nouvelles manières de manager, une des lectures phare à ne pas rater est « Managment 3.0 » de Jurgen Apello.
L’arrivée des méthodes agiles a transformé la manière de développer des solutions logicielles, mais le management a tendance à rester le même.
Jurgen Appello, à travers son livre, mais aussi un workshop dont Xebia nous fait un excellent retour, explique comment le rôle de manager doit évoluer pour trouver sa place au sein d’entreprises agiles.
<a title="http://blog.xebia.fr/2014/12/24/retour-sur-la-formation-management-3-0-workout-de-jurgen-appelo/" href="http://blog.xebia.fr/2014/12/24/retour-sur-la-formation-management-3-0-workout-de-jurgen-appelo/" target="_blank">http://blog.xebia.fr/2014/12/24/retour-sur-la-formation-management-3-0-workout-de-jurgen-appelo/</a></p>

<p style="text-align: justify;">Encore un article relatif à l’évolution du management en entreprise.
Points clés : moins de stress, moins de pression, créer un nouveau climat en se centrant sur l’humain.
<a title="http://www.huffingtonpost.fr/marc-dorel/conseil-actionnaires-entreprises_b_6300654.html?utm_hp_ref=tw" href="http://www.huffingtonpost.fr/marc-dorel/conseil-actionnaires-entreprises_b_6300654.html?utm_hp_ref=tw" target="_blank">http://www.huffingtonpost.fr/marc-dorel/conseil-actionnaires-entreprises_b_6300654.html?utm_hp_ref=tw</a></p>

<p style="text-align: justify;">Pour certaines personnes, Scrum ne s’applique pas forcement bien à l’entreprise dans son ensemble.
C’est pour cela que des « frameworks » ont fait leur apparition.
Ils essayent d’apporter des éléments supplémentaire pour adapter Scrum à toute l’entreprise, en y intégrant de nouvelles notions comme le Lean Thinking, les Feature teams, le nouveau rôle des managers …
<a title="LeSS" href="https://less.works/" target="_blank">LeSS</a> en fait partie.</p>

<p style="text-align: justify;">Très bon article sur la manière de construire son Product Backlog en pratiquant le Story Mapping
<a title="http://www.thoughtworks.com/insights/blog/story-mapping-visual-way-building-product-backlog" href="http://www.thoughtworks.com/insights/blog/story-mapping-visual-way-building-product-backlog" target="_blank">http://www.thoughtworks.com/insights/blog/story-mapping-visual-way-building-product-backlog</a></p>

<p style="text-align: justify;"><strong>TECH</strong></p>

<p style="text-align: justify;">Maven est l’outil d’automatisation de build le plus connu et utilisé des javaistes.
Cependant on entend de plus en plus parler de Gradle, basé sur le langage Groovy : fini le xml, on se rapproche d’une syntaxe plus épurée « à la Grunt ».
Ci-dessous un exemple de mise en place de Gradle dans le cadre d’un projet Java.
<a title="http://www.petrikainulainen.net/programming/gradle/getting-started-with-gradle-our-first-java-project/" href="http://www.petrikainulainen.net/programming/gradle/getting-started-with-gradle-our-first-java-project/" target="_blank">http://www.petrikainulainen.net/programming/gradle/getting-started-with-gradle-our-first-java-project/</a></p>

<p style="text-align: justify;">Encore pour nos amis javaistes, une liste de livres qu’il faut absolument lire quand on fait du développement Java.
Parmi ceux cités, je vous recommande Clean Code, celui sur le Refactoring by Martin Fowler et Effective Unit Testing ;)
<a title="http://www.petrikainulainen.net/reviews/10-books-every-java-developer-should-read/" href="http://www.petrikainulainen.net/reviews/10-books-every-java-developer-should-read/" target="_blank">http://www.petrikainulainen.net/reviews/10-books-every-java-developer-should-read/</a></p>

<p style="text-align: justify;">Quel que soit le langage ou le framework utilisé, les tests font partie intégrante du cycle de développement.
AngularJS ne déroge pas à la règle, d’autant plus que « Angular is written with testability in mind [..] ».
L’article qui suit aborde les bonnes pratiques de test d’une manière générale, contrairement à ce que le titre laisse supposer.
<a title="http://java.dzone.com/articles/top-5-mistakes-angularjs-failing-to-test" href="http://java.dzone.com/articles/top-5-mistakes-angularjs-failing-to-test" target="_blank">http://java.dzone.com/articles/top-5-mistakes-angularjs-failing-to-test</a></p>

<p style="text-align: justify;">Trop de Javascript peut tuer le Javascript.
Afin d’avoir une appli web performante, encore faut-il être capable de mesurer cette performance.
C’est ce que propose Sitespeed.io, écrit en Node, et qui répertorie tout un tas de métriques relatives aux perfs de vos pages webs.
<a title="http://blog.xebia.fr/2015/01/12/mesurer-la-performance-de-vos-pages-web-avec-sitespeed-io/" href="http://blog.xebia.fr/2015/01/12/mesurer-la-performance-de-vos-pages-web-avec-sitespeed-io/" target="_blank">http://blog.xebia.fr/2015/01/12/mesurer-la-performance-de-vos-pages-web-avec-sitespeed-io/</a></p>

<p style="text-align: justify;">Parmi les bonnes pratiques de code, le nommage des classes, méthodes, attributs … est selon moi une des plus importantes.
Comme le dit l’auteur de l’article qui suit, « code is read more often that it is written or changed ».
Bien nommer les choses permet une compréhension plus rapide du code et donc du fonctionnement global de l’application.
Les puristes diront même qu’un code clair et bien nommé peut faire office de documentation.
Quelques conseils ici =&gt; <a title="http://24ways.org/2014/naming-things/" href="http://24ways.org/2014/naming-things/" target="_blank">http://24ways.org/2014/naming-things/</a></p>

<p style="text-align: justify;">Petite réflexion sur <a title="l'utilité des interfaces" href="http://blog.cleancoder.com/uncle-bob/2015/01/08/InterfaceConsideredHarmful.html" target="_blank">l’utilité des interfaces</a> dans certains langages de programmation.
Qu’en penser … à méditer</p>

<p style="text-align: justify;">Tester ses interfaces graphiques, quand la pratique est mise en place, est bien souvent long et fastidieux.
Mais avec l’arrivée du Perceptual Testing, les choses s’améliorent.
Le principe : automatiser la comparaison de screenshots des différents écrans d’une version à l’autre, et détecter les différence.
C’est ce que notamment lesFurets.com a mis en place, et il parait que c’est plutôt efficace.
<a title="http://www.thoughtworks.com/insights/blog/perceptual-testing" href="http://www.thoughtworks.com/insights/blog/perceptual-testing" target="_blank">http://www.thoughtworks.com/insights/blog/perceptual-testing</a></p>

<p style="text-align: justify;"><strong>ET SINON</strong></p>

<p style="text-align: justify;">Comment une entreprise pratique la transparence totale, et même pour les salaires !
<a title="https://open.bufferapp.com/introducing-open-salaries-at-buffer-including-our-transparent-formula-and-all-individual-salaries/" href="https://open.bufferapp.com/introducing-open-salaries-at-buffer-including-our-transparent-formula-and-all-individual-salaries/" target="_blank">https://open.bufferapp.com/introducing-open-salaries-at-buffer-including-our-transparent-formula-and-all-individual-salaries/</a></p>

<p style="text-align: justify;">Tout le monde a déjà entendu parler de la Chine et du « pillage industriel ».
Cette pratique, plutôt mal vue dans les pays occidentaux, fait partie intégrante du process d’innovation des entreprises Chinoises.
En voici la vision.
<a title="http://www.thoughtworks.com/insights/blog/grow-brutally-how-china-approaches-innovation" href="http://www.thoughtworks.com/insights/blog/grow-brutally-how-china-approaches-innovation" target="_blank">http://www.thoughtworks.com/insights/blog/grow-brutally-how-china-approaches-innovation</a></p>

<p style="text-align: justify;">Enjoy !</p>
