---
layout: post
title: Arrête de jouer, deviens un ninja !
date: 2014-01-31 16:45
author: fabien maury
comments: true
categories: [java, maven, ninja, Outils, productivité, Programmation]
---
<p dir="ltr" style="text-align: justify;">Derrière ce titre <em>a priori</em> sans aucun sens se cache une présentation de mon dernier coup de cœur: un framework web full stack pour java.</p>

<p dir="ltr" style="text-align: justify;">Là je vous entends dire: tiens tiens...il veut nous parler de Play! celui là ?</p>

<p dir="ltr" style="text-align: justify;">Et bien non, je veux vous parler de ....<a href="http://www.ninjaframework.org/">
</a></p>

<p dir="ltr" style="text-align: justify;"><span style="color: #0000ff;"><a style="font-size: 30px; line-height: 1.1;" href="http://www.ninjaframework.org/"><span style="color: #0000ff;">Ninja web framework</span></a></span></p>

<p dir="ltr" style="text-align: justify;"><a title="Projet open-source" href="https://github.com/ninjaframework/ninja" target="_blank">Projet open-source</a> initié en 2012, ce framework prendrait son origine chez des utilisateurs de Play 1 qui auraient trouvé Play 2 trop "Scala". En résumé cela donne un framework s'inspirant de Rails et Play mais avec une stack 100% Java.</p>

<p style="text-align: justify;">Il est depuis peu en version 3.0.0</p>

<blockquote class="twitter-tweet" lang="fr">Ninja 3.0.0-rc1 released! Give it a try!

— Ninja Framework Team (@ninjaframework) <a href="https://twitter.com/ninjaframework/statuses/429210077321371648">31 Janvier 2014</a></blockquote>

<p style="text-align: justify;"><script charset="utf-8" type="text/javascript" src="//platform.twitter.com/widgets.js" async=""></script>
Leur site web en a profité pour se faire une beauté, et compléter sa documentation.</p>

<h2 dir="ltr" style="text-align: justify;"><!--more-->Composition</h2>

<p dir="ltr" style="text-align: justify;">Tout d'abord, votre projet ninja est un projet Maven de facto, vous ne devriez donc pas être perdus.</p>

<p dir="ltr" style="text-align: justify;">La création du projet se fait effectivement via un archétype maven et si on regarde la liste des fonctionnalités on retrouve des noms bien connus du monde java:</p>

<ul style="text-align: justify;">
    <li>Html templating/rendering avec <strong>freemarker</strong></li>
    <li>Migration de base de donnée avec <strong>FlyWay</strong></li>
    <li>Injection de dépendance avec <strong>Guice</strong></li>
    <li>Validation avec la <strong>JSR 303</strong> (Hibernate-validation)</li>
    <li>Logs avec <strong>logback et slf4j</strong></li>
    <li>Sérialisation/dé-sérialisation (Json,Xml) avec <strong>Jackson</strong></li>
    <li><strong>Mockito</strong> et <strong>FluentLenium</strong></li>
</ul>

<p style="text-align: justify;">On profite de surcroît de tous les avantages de <strong>Maven</strong> (gestion de dépendance, build, etc.).</p>

<p dir="ltr" style="text-align: justify;">A savoir qu'il ne s'agit pas seulement d'un archétype, mais bien d'une stack complète.</p>

<p dir="ltr" style="text-align: justify;">En effet la couche Ninja apporte elle-même de nombreuses fonctionnalités sympathiques (<a href="http://www.ninjaframework.org/documentation/internationalization.html" target="_blank">i18n</a>, <a href="http://www.ninjaframework.org/documentation/scheduler.html" target="_blank">scheduler</a>, Filtres) mais surtout de quoi tester dans tous les sens:</p>

<ul style="text-align: justify;">
    <li>NinjaRouterTest, pour tester ses routes</li>
    <li>NinjaTest, pour tester sur une application déployée</li>
    <li>NinjaDocTester, pour tester et documenter vos Api</li>
    <li>Pour le reste vous avez à disposition <strong>Mockito</strong></li>
</ul>

<h2 dir="ltr" style="text-align: justify;">Modules</h2>

<p dir="ltr" style="text-align: justify;">A l'instar de Play, on peut agrémenter sa stack ninja de <a href="http://www.ninjaframework.org/documentation/modules.html" target="_blank">modules</a>. Pour l'instant la liste est courte, ce qui s'explique par la jeunesse du framework.</p>

<h1 dir="ltr" style="text-align: justify;">Plus concrètement</h1>

<p dir="ltr" style="text-align: justify;">Différents archétypes sont à votre disposition:</p>

<ul style="text-align: justify;">
    <li>ninja-core-demo-archetype, un showroom du framework: beaucoup de nettoyage à faire si vous l'utilisez comme base</li>
    <li>ninja-jpa-demo-archetype, un projet d'exemple plus simple, comprenant juste un écran avec lien vers base de données (dao,migration): c'est celui que j'utilise pour créer mes projet</li>
    <li>ninja-appengine-blog-archetype, un moteur de blog basique fonctionnant sur app-engine</li>
</ul>

<p style="text-align: justify;">Il existe d'autres archétypes (plusieurs variantes à chaque fois: blog,demo,core-demo) mais tous ne sont pas à jours avec la dernière version de ninja, il faudra donc quelques fois se référer à la page de mise à jour de version.</p>

<p style="text-align: justify;">Nous allons intégrer un projet Ninja dans un projet <a href="https://github.com/cafetux/ninja-todolist/tree/origin-project">Maven existant</a> composé d'un pom parent et d'un module contenant les dtos (potentiellement partagé avec une application cliente: android ou autre). Le modèle est composé d'une simple classe TaskDto (une tâche, afin de gérer une todolist).</p>

<p style="text-align: justify;">Allons dans le root de ce projet Maven de type pom (packaging), et exécutons la commande suivante</p>

[bash]
mvn archetype:generate -DarchetypeGroupId=org.ninjaframework -DarchetypeArtifactId=ninja-servlet-jpa-blog-archetype -DarchetypeVersion=2.5.1
[/bash]

<p style="text-align: justify;">Note: la version 3 des archétypes n'est pas encore sortie, mais la montée de version se fait aisément dans le pom.</p>

<p dir="ltr" style="text-align: justify;">La création étant interactive, nous renseignons les informations suivantes:</p>

<blockquote>groupId: fr.arolla.ninja
artifactId: ninja-todo-backend
version: 1.0-SNAPSHOT
package: fr.arolla.ninja</blockquote>

<p dir="ltr" style="text-align: justify;">Nous appelons ce sous-projet ninja-todo-backend, et vérifions qu'il est bien ajouté à la liste des modules du projet parent.</p>

<p dir="ltr" style="text-align: justify;">Un simple <em><strong>mvn clean install </strong></em>permet de vérifier que le sous-projet ninja a été correctement intégré au projet parent.</p>

<p dir="ltr" style="text-align: justify;">Nous pouvons aussi vérifier que le projet ninja se lance:</p>

<p dir="ltr" style="text-align: justify;"><em><strong>cd ninja-todo-backend</strong></em></p>

<p dir="ltr" style="text-align: justify;"><strong><em>mvn ninja:run</em></strong></p>

<p dir="ltr" style="text-align: justify;">L'application est alors accessible à l'adresse <em>http://localhost:8080/</em></p>

<p dir="ltr" style="text-align: justify;">Ouvrons le module <strong>ninja-todo-backend</strong> et découvrons différents répertoires:</p>

<p dir="ltr" style="text-align: justify;">/src/main/java:</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> assets : répertoire contenant les ressources statiques telles que les images, les fichiers css, etc.</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> conf : les fichiers de propriétés, l'internationalisation, les routes, etc.</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> controllers : c'est le code java sur lesquels sont mappés vos routes définies dans le fichier conf/Routes.java</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> db/migration : les fichiers de migration sql, un pour chaque version</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> models : rien de bien original, ce sont les classes du modèle</p>

<p dir="ltr" style="text-align: justify;"><img alt="folder" src="http://www.arolla.fr/blog/wp-content/uploads/2014/01/folder.png" width="20" height="20" /> views : les vues au format freemarker, un sous répertoire par Controller comprenant un template par fonction</p>

<h2 dir="ltr" style="text-align: justify;">Une approche MVC</h2>

<p dir="ltr" style="text-align: justify;">Commençons par ajouter notre module de modèle au projet ninja-todo-backend (dans le fichier pom.xml), puis ajoutons un écran affichant une liste de tâches à accomplir.</p>

<p dir="ltr" style="text-align: justify;">Commençons par ajouter notre table dans le fichier db/migration/V1__.sql, qui est le script initial joué par flyway.</p>

[sql]
create table Tasks (
id bigint generated by default as identity,
title varchar(255),
description varchar(255),
doneAt timestamp,
dueDate timestamp,
primary key (id));
[/sql]

<p dir="ltr" style="text-align: justify;">Attelons nous ensuite à la partie C (le controller) en créant un simple controller avec une méthode <strong><em>retrieveAllTasks()</em></strong>, dans le répertoire <strong>controller:</strong></p>

[java]
@Singleton
public class TaskListController {

    @Inject
    Provider&lt;EntityManager&gt; entitiyManagerProvider;
    @Inject
    private static final Logger LOGGER= LoggerFactory.getLogger(TaskListController.class);

    private Function&lt;Task,TaskDto&gt; TO_DTO = new Function&lt;Task, TaskDto&gt;() {
        @Override
        public TaskDto apply(Task task) {
            TaskDto dto = new TaskDto(task.getDueDate(), task.getDescription(), task.getTitle());
            if (task.getDoneAt() != null) {
                dto.done();
            }
            return dto;
        }
    };

    @Transactional
    public Result retrieveAllTasks() {
            List&lt;TaskDto&gt; tasks= retrieveTasks();
            return Results
                .html()
                .render(&quot;tasks&quot;, tasks);
    }

    private List&lt;TaskDto&gt; retrieveTasks() {
        EntityManager entityManager = entitiyManagerProvider.get();

        TypedQuery&lt;Task&gt; query= entityManager.createQuery(&quot;SELECT x FROM Task &quot;, Task.class);
        List&lt;Task&gt; tasks = query.getResultList();

        return Lists.transform(tasks, TO_DTO);
    }
[/java]

<p style="text-align: justify;">Il nous manque à router ce controller, en mappant une route sur cette fonction dans le fichier conf/Routes.java:</p>

[java]
router.GET().route(&quot;/tasks&quot;).with(TaskListController.class, &quot;retrieveAllTasks&quot;);
[/java]

<p style="text-align: justify;">Le controller fait appel à la fonction 'render' pour générer le rendu au format html, qui prend en paramètre une liste nommée <strong>'tasks'. </strong>Nous allons donc devoir créer la vue correspondante dans le répertoire <strong><em>views/{NomController}/{nomFonction}.ftl.html</em></strong> (un template freemarker), ce qui dans notre cas donnera le fichier <strong>views/TaskListController/retrieveAllTasks.ftl.html:</strong></p>

[html]
&lt;#import &quot;../layout/defaultLayout.ftl.html&quot; as layout&gt;
&lt;@layout.myLayout &quot;Home page&quot;&gt;
&lt;h1&gt;Waiting tasks&lt;/h1&gt;
&lt;#if !tasks?has_content&gt;
    &lt;p&gt;No Tasks entries yet&lt;/p&gt;
&lt;#else&gt;
    &lt;h1&gt;Tasks&lt;/h1&gt;
    &lt;#list tasks as task&gt;
        &lt;div class=&quot;jumbotron&quot;&gt;
            &lt;h2&gt;${task.title}&lt;/h2&gt;
            &lt;p&gt;${task.description}&lt;/p&gt;
        &lt;/div&gt;
    &lt;/#list&gt;
&lt;/#if&gt;
&lt;/@layout.myLayout&gt;
[/html]

<p style="text-align: justify;">Accédez à votre page dans votre navigateur (http://localhost:8080/tasks). Il n'y a aucune tâche pour l'instant.</p>

<p style="text-align: justify;">Ninja framework propose un tips assez utile. En effet nous trouvons dans le package conf une classe StartupActions qui contient la fonction suivante</p>

[java]
    @Start(order=100) //ordre de priorité basse
    public void generateDummyDataWhenInTest() {
       if (!ninjaProperties.isProd()) {
            setupDao.setup();
        }
    }
[/java]

<p style="text-align: justify;">Nous allons donc pouvoir remplir avec quelques tâches notre base au démarrage (et uniquement en phase de développement) en rajoutant des données dans la classe SetupDao:</p>

[java]
entityManager.persist(new Task(&quot;Article Blog Ninja&quot;, &quot;faire un court article pour parler du framework ninja&quot;;));
entityManager.persist(new Task(&quot;Jam de code&quot;,&quot;s'inscrire à la prochaine Jam de code Arolla&quot;));
[/java]

<p style="text-align: justify;">Relancez le server, un liste de taches apparaît maintenant.</p>

<h2 dir="ltr" style="text-align: justify;">Exposez facilement des api REST</h2>

<p style="text-align: justify;">Effectivement exposer les mêmes données en json est simple, il vous suffit d'ajouter une route:</p>

[java]
router.GET().route(&quot;/tasks/json&quot;).with(TaskListController.class, &quot;retrieveAllTasksJson&quot;);
[/java]

<p style="text-align: justify;">Ainsi qu'une fonction dans le controller retournant du json:</p>

[java]
    @Transactional
    public Result retrieveAllTasksJson() {
        List&lt;TaskDto&gt; tasks= retrieveTasks();
        return Results
                .json()
                .render(tasks);
    }
[/java]

<p style="text-align: justify;">Et nous pouvons retrouver nos tâches au format json.</p>

<p style="text-align: justify;">La documentation est très bien faite, il est donc facile de monter en compétence sur les différentes facettes du framework.
Je n'en montrerai donc pas plus car il serait assez difficile de faire mieux que le site officiel.</p>

<h1 dir="ltr" style="text-align: justify;">Conclusion</h1>

<p style="text-align: justify;">Le framework ninja est un bon framework, bien pensé avec lequel on est vraiment productif.
L'objectif est donc bien atteint.
Son intégration directement dans Maven est je pense un gros atout.
Il est juste dommage qu'il ne soit pas arrivé quelques années plus tôt, il aurait surement fait une entrée plus fracassante.
Dans un contexte où Play est vraiment implanté sur ce créneau, et à l'heure où certaines personnes se détournent de Java et de Maven, il répond cependant à un certain besoin et à mon avis il trouvera quand même son public.</p>

<p style="text-align: justify;">Les sources sont disponibles <a href="https://github.com/cafetux/ninja-todolist">ici.</a></p>
