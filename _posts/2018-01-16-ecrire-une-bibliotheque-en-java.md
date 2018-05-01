---
layout: post
title: Écrire une bibliothèque en Java
date: 2018-01-16 12:16
author: christophe michel
comments: true
categories: [Non classé]
---
<h1>Écrire une bibliothèque en Java</h1>

Cette fois ça y est. Ce petit bout de code Java bien pratique que vous avez terminé récemment, vous aimeriez bien le partager avec le monde entier. Parce qu’il simplifie la vie, parce qu’il est différent de l’existant, ou meilleur, ou plus simple d’utilisation.

Quelle que soit votre motivation, ce que vous voulez, c’est écrire une bibliothèque en Java. La bonne nouvelle, c’est que ça reste le code Java dont vous avez l’habitude, mais si votre job de tous les jours consiste à écrire ou maintenir des applications, il y a un certain nombre de différences notables entre le moment où ça compile pour la première fois et les millions (que dis-je, les milliards!) de téléchargements sur Maven Central.

Dans cet article, je partirai du principe que vous publierez votre bibliothèque en open source et vous présenterai la marche à suivre pour mener ce type de projet.

<h2>Le code</h2>

Tant que votre code reste confiné dans votre projet, vous faites un peu ce que vous voulez. Dans les limites du raisonnable bien sûr, on n’est pas des bêtes, mais à partir du moment où vous ne distribuez pas le code, vous gardez une grande liberté d’écriture.

<h3>Dépendances</h3>

En revanche, lorsque vous commencez à être inclus dans les projets de tout un tas de monde, c'est une autre paire de manches!

A défaut d'écrire la bibliothèque sous forme de module (fonctionnalité introduite en Java 9), toutes les dépendances que vous intégrez dans votre bibliothèque seront transitivement présentes dans l'application de vos utilisateurs.

Si les versions que vous utilisez sont incompatibles avec les leurs, bienvenue dans le monde du <a href="https://blog.codefx.org/java/jar-hell/">jar hell</a> et des exclusions de dépendances. Personne n'a envie de s'infliger ça et trimbaler trop de dépendances dans votre bibliothèque risque de décourager autrui de s'en servir.

Les <a href="https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/">déboires encore récents</a> des projets JavaScript <a href="http://www.haneycodes.net/npm-left-pad-have-we-forgotten-how-to-program/">reposant sur des myriades de micro-dépendances</a> doivent nous rappeler que les limiter au maximum est une bonne idée dans n'importe quel projet et une absolue nécessité pour une bibliothèque.

<h4>Logging</h4>

Un cas particulier de dépendances, c’est le logging. C’est une bonne idée d'en prévoir dans votre bibliothèque car bien conçu, il sera d’une aide précieuse à vos utilisateurs (et pourra vous servir aussi au passage).

Sauf que le framework de logging, c’est une dépendance et qu’il serait assez inconvenant d’imposer le vôtre aux utilisateurs de votre bibliothèque. Le problème, c’est qu’à moins de vous restreindre à utiliser <code>java.util.logging</code>, vous allez bien devoir écrire votre code de logging avec un framework.

Heureusement, vous n’êtes pas le premier à rencontrer ce cas de figure et la solution est simple : utiliser une façade de logging. La référence actuelle est <a href="https://www.slf4j.org/">SLF4J</a>, qui peut s’interfacer avec la plupart des implémentations populaires, telles que <a href="https://logging.apache.org/log4j/">Log4J</a> ou <a href="https://logback.qos.ch/">Logback</a>. Elle se branchera sans problème sur celle de vos utilisateurs.

<h4>Injection de dépendance</h4>

La question de l’injection de dépendance est un peu plus délicate. Il est bien évidemment hors de question d’embarquer un framework “lourd” à la Spring pour assembler les classes de votre bibliothèque.

Il existe toute une gamme de frameworks plus simples, mais dans le cadre d’un projet de ce type, il est tout à fait possible de s’en passer en revenant aux bonnes vieilles méthodes : des Factory et des Builder.

Veillez si possible à préserver l’extensibilité de votre bibliothèque en évitant les factory method statiques qui ne permettent pas de dériver les classes de votre API publique. Utilisez-les plutôt pour certaines dépendances techniques qu'il sera facile de d'instancier à part.

[code lang=java]
public class MyApi {

    // Méthode statique impossible à étendre
    public static MyApi getInstance() {
        return new MyApi();
    }

    private MyApi() {
        // Initialisation ...
    }

}

MyApi api = MyApi.getInstance();
[/code]

Ici, sous-classer <code>MyApi</code> est possible, mais vous perdez le code d'initialisation, ce qui risque de poser problème. On préférera la construction plus standard, si possible en injectant un objet de configuration.

[code lang=java]
public class MyApi {

    private final MyApiConfiguration configuration;

    public MyApi() {
        this(MyApiConfiguration.getDefault());
    }

    public MyApi(MyApiConfiguration configuration) {
        this.configuration = configuration;
        // Initialisation
    }

}

MyAPI api = new MyApi();
[/code]

<h3>La compatibilité</h3>

En écrivant une bibliothèque, vous allez distribuer du code et perdre le contrôle sur son utilisation. Vos utilisateurs vont étendre vos classes, s’en servir de façon imprévue et ne mettront pas forcément à jour le package quand vous le souhaiterez.

L'idéal pour une bibliothèque est d'utiliser <a href="https://semver.org/lang/fr/">SemVer</a>, la gestion sémantique de versions. Le principe général de SemVer est le suivant :

<ul>
<li>Un changement de version majeure indique des modifications non rétrocompatibles de votre API publique</li>
<li>Un changement de version mineure indique des modifications rétrocompatibles de votre API publique</li>
<li>Un changement de version corrective indique des corrections d'anomalies rétrocompatibles</li>
</ul>

Dans le cadre d'une bibliothèque Java, la compatibilité s'entend au sens de la <a href="https://docs.oracle.com/javase/specs/jls/se9/html/jls-13.html">compatibilité binaire</a>. Si une version de votre bibliothèque est rétrocompatible avec une version antérieure, le code continue de compiler, mais aussi de linker et de s'exécuter sans erreur.

Par exemple, renommer un champ privé ou rendre une méthode privée publique sont des changements rétrocompatibles. Supprimer une méthode publique ou changer un nom de package ne le sont pas. La documentation de la JVM dresse une liste exhaustive de ces changements.

Si jamais vous désirez que la cohabitation entre deux versions majeures de votre bibliothèque soit possible, vous devrez changer les noms de package Java et le Group Id Maven pour éviter toute collusion. C'est par exemple ce qu'a fait la bibliothèque <a href="https://github.com/FasterXML/jackson">Jackson</a> en passant de <code>org.codehaus.jackson</code> à <code>com.fasterxml.jackson</code>.

<h2>La licence</h2>

Il est possible de publier son code ou ses binaires sans licence, mais si vous le faites, c’est le droit basique du copyright qui s’applique et dans ce cas <a href="https://choosealicense.com/no-permission/">personne ne pourra utiliser votre création sans votre autorisation expresse</a>. Autant dire que c’est un frein conséquent pour un utilisateur.

Il existe une large gamme de licences pour l’open source, qui couvrent de nombreuses sensibilités et de nombreuses nuances. Le choix est un peu difficile pour le béotien mais heureusement on nous mâche régulièrement le travail avec des assistants de sélection comme <a href="https://choosealicense.com/">celui de GitHub</a>.

<h2>L'outillage</h2>

<h3>Gestion de version</h3>

Partons du principe que vous utilisez <em>git</em>. Les plateformes les plus populaires  <a href="https://github.com">GitHub</a>, <a href="https://gitlab.com">GitLab</a> et <a href="https://bitbucket.com">Bitbucket</a>. Pour un projet personnel open source, les trois sont relativement proches, à quelques différences près :

<ul>
<li>Bitbucket et GitLab proposent des dépôts privés dans leur formule gratuite</li>
<li>GitHub dispose d'une communauté open source très importante et d'intégration avec un plus grand nombre de services tiers que ses concurrents</li>
</ul>

<h3>Intégration continue</h3>

Le choix de l'outil d'intégration continue, à moins de l'héberger soi-même, dépend en partie du choix de la solution de gestion de version.

Si vous avez opté pour GitLab ou BitBucket, ces services proposent des solutions d'intégration continue (<a href="https://about.gitlab.com/features/gitlab-ci-cd/">GitLab CI/CD</a> et <a href="https://bitbucket.org/product/features/pipelines">BitBucket Pipelines</a> respectivement) et le plus simple reste de les utiliser.

Si vous avez opté pour GitHub, le choix le plus populaire est <a href="https://travis-ci.org/">Travis</a>, auquel vous vous connecterez directement avec votre compte GitHub pour un accès immédiat à vos dépôts.

Travis est piloté par un fichier de configuration <code>.travis.yml</code> à placer à la racine du projet. Pour un projet Java 8 simple, le contenu est minimal :

[code lang=text]
language: java
jdk: oraclejdk8
cache:
    directories:
        - $HOME/.m2
[/code]

La mise en cache du dépôt Maven local est vitale pour éviter des temps de build excessifs.

Une fois le projet configuré et buildé, vous pouvez modifier le <code>README.md</code> du projet pour <a href="https://docs.travis-ci.com/user/status-images/">afficher sur sa page d'accueil un badge qui indique le statut du build</a>.

<h3>Qualité de code</h3>

Outre la possibilité d'embarquer des plugins de type <a href="http://findbugs.sourceforge.net/">FindBugs</a> ou <a href="https://pmd.github.io/">PMD</a> directement dans le build, il existe des outils en ligne pour obtenir des indicateurs sur la qualité de code du projet.

Le plus populaire de tous est <a href="https://about.sonarcloud.io/">SonarQube</a> qui s'interface avec un très grand nombre d'usines d'intégration continue et propose une version en ligne gratuite pour les projets open source.

Si vous avez choisi la combinaison GitHub/Travis, la mise en place est extrêmement simple (là encore, il est possible d'utiliser son compte GitHub pour se connecter à SonarQube) et elle est détaillée dans la <a href="https://docs.travis-ci.com/user/sonarcloud/">documentation de Travis</a>.

Si vous avez opté pour d'autres services, ils disposent généralement de plugins ou d'intégrations dédiés à Sonar.

Si vous n'êtes intéressé que par la couverture de tests, <a href="https://coveralls.io/">Coveralls</a> est une bonne alternative. Il s'intègre actuellement avec les projets GitHub et Bitbucket, le support GitLab étant prévu à l'avenir.

<h2>Publication sur Maven Central</h2>

La publication de votre jar sur <a href="http://central.sonatype.org/">Maven Central</a> pourrait faire l’objet d’un article à part entière. Il y a un certain nombre d’étapes à respecter, en voici un aperçu :

<ol>
<li>Inscrire votre projet via un <a href="https://issues.sonatype.org">ticket JIRA</a> chez Sonatype</p></li>
<li><p>Générer une clé <a href="https://www.gnupg.org/">GPG</a> et paramétrer la signature automatique de vos livrables dans le build</p></li>
<li><p>Déployer sur Maven Central</p></li>
</ol>

<p>Pour le guide complet, je vous renvoie vers <a href="http://central.sonatype.org/pages/ossrh-guide.html">le guide de Sonatype</a> (qui héberge le dépôt) et plus spécifiquement <a href="http://central.sonatype.org/pages/apache-maven.html">le processus de déploiement avec Maven</a>.

Idéalement, vous voudrez que la génération des jars de code source, de Javadoc et la signature GPG des livrable soient des étapes associées à un profil de release qui n’est exécuté que lorsque vous effectuez la release. Non seulement vous gagnez du temps sur vos builds, mais cela vous évite une étape inutile lorsque vous faites de l’intégration continue, sur Travis par exemple.

<h2>Documentation</h2>

La documentation est importante dans n’importe quel projet de développement, mais elle est absolument vitale pour une bibliothèque.

Non seulement vous devrez vous assurer que le code lui-même est correctement documenté (que ce soit via une documentation explicite type Javadoc, un nommage approprié ou encore les tests) mais vous devrez également fournir une documentation d’utilisation.

<blockquote>
  “After just a few user testing sessions, it became clear that developers expected to learn how to work with a new SDK from examples.” — <a href="https://medium.com/google-design/how-i-do-developer-ux-at-google-b21646c2c4df">How I do Developer UX at Google</a>
</blockquote>

En effet, une des premières choses qu’un développeur va regarder avant de choisir d’utiliser une bibliothèque, c’est la façon dont elle s’utilise. Les exemples se doivent d’être clairs et abondants.

Écrivez une section “Quick Start” qui permet de commencer tout de suite à utiliser votre code sur un cas simple et n'hésitez pas à donner des cas plus détaillés.

Donnez les moyens aux développeurs de savoir rapidement et de façon fiable si l’outil que vous proposez leur est adapté. Il est souvent plus rapide de chercher une bibliothèque alternative à la vôtre que de passer trop de temps à comprendre ce qu'elle peut offrir ou comment elle fonctionne.

<h3>Contributions au projet</h3>

Puisque votre projet est open source, vous souhaitez peut-être que d'autres personnes contribuent au projet. Dans ce cas, vous devrez fournir des instructions claires sur le processus de contribution. Ces instructions incluent par exemple :

<ul>
<li>Comment soumettre une modification de code</li>
<li>Comment signaler un bug</li>
<li>Comment contacter le mainteneur du projet</li>
<li>Où trouver la roadmap s'il y en a une</li>
</ul>

GitHub fournit <a href="https://help.github.com/articles/helping-people-contribute-to-your-project/">un guide pour faciliter les contributions à un projet</a> qui couvre ces aspects et bien d'autres encore. Certains font même l'objet de fonctionnalités spécifiques sur GitHub, qu'on peut également trouver chez ses concurrents.

<h2>Lancez-vous !</h2>

A ce stade, vous avez toutes les informations et pistes nécessaires pour démarrer votre projet. Reste quelque chose que l'on peut difficilement faire à votre place, c'est trouver une bonne idée et avoir la discipline de travailler dessus régulièrement. Qui sait, vous créerez peut-être un futur élément incontournable de l'écosystème Java ? Et sans aller jusque là, un projet de ce type est une bonne façon de démontrer vos compétences à un client ou un employeur potentiel.

N'hésitez pas à partager vos propres expériences sur le sujet dans les commentaires ou sur Twitter @christopheml
