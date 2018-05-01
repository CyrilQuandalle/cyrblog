---
layout: post
title: Le BDD mis en oeuvre avec JBehave
date: 2012-06-21 11:00
author: arnauld loyer
comments: true
categories: [Agilité, bdd, Bonnes pratiques de dév, java, jbehave, junit, maven, Outils, Programmation]
---
<section>
<p style="text-align: justify">Dans notre précédent article (<a href="http://www.arolla.fr/blog/?p=707">Le BDD qu'est ce que c'est?</a>), nous avons vu ce qu'était le <strong>BDD</strong>, son intérêt et le formalisme généralement adopté. Pas mal de théorie pour placer le contexte, tout ça c'est bien, mais ça manquait un peu de code, du graisseux !</p>
<p style="text-align: justify"><strong>Allez, on code !</strong></p>

<h2 style="text-align: justify">Mise en place de notre environnement</h2>
<p style="text-align: justify">Commençons par créer un nouveau projet Maven et ajoutons les dépendances nécessaires dans notre descripteur de projet (pom.xml):</p>

</section>

[xml]
&lt;project xmlns=&quot;http://maven.apache.org/POM/4.0.0&quot; 
  xmlns:xsi=&quot;http://www.w3.org/2001/XMLSchema-instance&quot;
  xsi:schemaLocation=&quot;http://maven.apache.org/POM/4.0.0 
                      http://maven.apache.org/xsd/maven-4.0.0.xsd&quot;&gt;

  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;jbehave-get-started&lt;/groupId&gt;
  &lt;artifactId&gt;bdd101&lt;/artifactId&gt;
  &lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;

  &lt;!-- ************************************************ --&gt;
  &lt;!-- *~~~~~~~~~~~~~~~~~PROPERTIES~~~~~~~~~~~~~~~~~~~* --&gt;
  &lt;!-- ************************************************ --&gt;
  &lt;properties&gt;
    &lt;maven.compiler.source&gt;1.6&lt;/maven.compiler.source&gt;
    &lt;maven.compiler.target&gt;1.6&lt;/maven.compiler.target&gt;
    &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;

    &lt;!-- lib versions --&gt;
    &lt;hamcrest.version&gt;1.2&lt;/hamcrest.version&gt;
    &lt;spring.version&gt;3.1.1.RELEASE&lt;/spring.version&gt;
    &lt;slf4j.version&gt;1.6.4&lt;/slf4j.version&gt;
    &lt;jbehave.version&gt;3.6.6&lt;/jbehave.version&gt;
  &lt;/properties&gt;

  &lt;!-- ************************************************ --&gt;
  &lt;!-- *~~~~~~~~~~~~~~~~DEPENDENCIES~~~~~~~~~~~~~~~~~~* --&gt;
  &lt;!-- ************************************************ --&gt;
  &lt;dependencies&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~Commons~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.commons&lt;/groupId&gt;
      &lt;artifactId&gt;commons-lang3&lt;/artifactId&gt;
      &lt;version&gt;3.1&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~Spring~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.springframework&lt;/groupId&gt;
      &lt;artifactId&gt;spring-context&lt;/artifactId&gt;
    &lt;/dependency&gt;

    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~Log~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
      &lt;artifactId&gt;log4j-over-slf4j&lt;/artifactId&gt;
      &lt;version&gt;${slf4j.version}&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;ch.qos.logback&lt;/groupId&gt;
      &lt;artifactId&gt;logback-classic&lt;/artifactId&gt;
      &lt;version&gt;1.0.0&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~JBehave~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.jbehave&lt;/groupId&gt;
      &lt;artifactId&gt;jbehave-core&lt;/artifactId&gt;
      &lt;version&gt;${jbehave.version}&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;org.jbehave&lt;/groupId&gt;
      &lt;artifactId&gt;jbehave-spring&lt;/artifactId&gt;
      &lt;version&gt;${jbehave.version}&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;de.codecentric&lt;/groupId&gt;
      &lt;artifactId&gt;jbehave-junit-runner&lt;/artifactId&gt;
      &lt;version&gt;1.0.1-SNAPSHOT&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~Test~~~~~~~~~~~~~~~~ --&gt;
    &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit-dep&lt;/artifactId&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.hamcrest&lt;/groupId&gt;
      &lt;artifactId&gt;hamcrest-library&lt;/artifactId&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.hamcrest&lt;/groupId&gt;
      &lt;artifactId&gt;hamcrest-core&lt;/artifactId&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;

  &lt;!-- ************************************************ --&gt;
  &lt;!-- *~~~~~~~~~~~DEPENDENCY MANAGEMENT~~~~~~~~~~~~~~* --&gt;
  &lt;!-- ************************************************ --&gt;
  &lt;dependencyManagement&gt;
    &lt;dependencies&gt;
      &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
      &lt;!-- ~~~~~~~~~~~~~~Spring~~~~~~~~~~~~~~~ --&gt;
      &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
      &lt;dependency&gt;
        &lt;groupId&gt;org.springframework&lt;/groupId&gt;
        &lt;artifactId&gt;spring-context&lt;/artifactId&gt;
        &lt;version&gt;${spring.version}&lt;/version&gt;
      &lt;/dependency&gt;

      &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
      &lt;!-- ~~~~~~~~~~~~~~~Test~~~~~~~~~~~~~~~~ --&gt;
      &lt;!-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ --&gt;
      &lt;dependency&gt;
        &lt;groupId&gt;junit&lt;/groupId&gt;
        &lt;artifactId&gt;junit-dep&lt;/artifactId&gt;
        &lt;version&gt;4.10&lt;/version&gt;
      &lt;/dependency&gt;
      &lt;dependency&gt;
        &lt;groupId&gt;org.hamcrest&lt;/groupId&gt;
        &lt;artifactId&gt;hamcrest-library&lt;/artifactId&gt;
        &lt;version&gt;${hamcrest.version}&lt;/version&gt;
      &lt;/dependency&gt;
      &lt;dependency&gt;
        &lt;groupId&gt;org.hamcrest&lt;/groupId&gt;
        &lt;artifactId&gt;hamcrest-core&lt;/artifactId&gt;
        &lt;version&gt;${hamcrest.version}&lt;/version&gt;
      &lt;/dependency&gt;
    &lt;/dependencies&gt;
  &lt;/dependencyManagement&gt;

  &lt;!-- ************************************************ --&gt;
  &lt;!-- *~~~~~~~~~~~~~~~~~REPOSITORIES~~~~~~~~~~~~~~~~~* --&gt;
  &lt;!-- ************************************************ --&gt;
  &lt;repositories&gt;
    &lt;repository&gt;
      &lt;id&gt;codehaus-releases&lt;/id&gt;
      &lt;name&gt;Codehaus Nexus Repository Manager&lt;/name&gt;
      &lt;url&gt;https://nexus.codehaus.org/content/repositories/releases/&lt;/url&gt;
    &lt;/repository&gt;
    &lt;repository&gt;
      &lt;id&gt;sonatype-snapshots&lt;/id&gt;
      &lt;name&gt;Sonatype Snapshots&lt;/name&gt;
      &lt;url&gt;https://oss.sonatype.org/content/repositories/snapshots/&lt;/url&gt;
    &lt;/repository&gt;
  &lt;/repositories&gt;

&lt;/project&gt;
[/xml]

<section>
<p style="text-align: justify">On notera les dépendances à <code>JBehave</code>, quelques utilitaires pour simplifier l'écriture de nos tests,et <code>Spring</code> pour l'injection de dépendance.</p>
<p style="text-align: justify">On retiendra aussi la dépendance à <code>jbehave-junit-runner</code> qui permet une intégration encore plus riche avec Junit en utilisant un lanceur spécial: <code>de.codecentric.jbehave.junit.monitoring.JUnitReportingRunner</code>. <strong>Ce lanceur permet de visualiser chaque étape de chaque scénario comme un test spécifique, il est ainsi beaucoup plus facile d'identifier à quelle étape notre scénario a échoué.</strong> De plus, cela s'intègre parfaitement avec la vue Eclipse, JUnit permettant un retour immédiat lorsque les tests sont exécutés directement depuis l'IDE. La page du projet correspondant peut être trouvée ici: <a href="https://github.com/codecentric/jbehave-junit-runner">Code Centric ~ jbehave-junit-runner</a></p>
<p style="text-align: justify">Comme nous nous baserons uniquement sur les annotations <em>Spring</em> pour l'injection de dépendances et la définition de nos étapes, nous nous passerons de fichier de configuration <em>Spring</em>. Le contexte sera directement initialisé par la méthode suivante:</p>

</section>

[java]
public static AnnotationConfigApplicationContext createContextFromBasePackages(String... basePackages) {
	AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
	applicationContext.scan(basePackages);
	applicationContext.refresh();
	return applicationContext;
}
[/java]

<section>
<p style="text-align: justify">Voila pour l'infrastructure, il nous reste à définir la classe qui lancera nos scénarios. Nous nous baserons pour cela sur le framework <em>JUnit</em> pour lequel jbehave fournit les adaptateurs nécessaires.</p>
<p style="text-align: justify">Au risque de faire un peu peur au début, nous opterons tout de suite pour une description assez riche de notre environnement de tests. <em>JBehave</em> fournit de multiples façons diverses et variées pour configurer l'environnement d'exécution des scénarios, nous choisissons ici la moins "magique" mais la plus verbeuse et surtout celle qui permet un contrôle total de chaque composant.</p>

</section>

[java]
...

import bdd101.util.Springs;
import bdd101.util.UTF8StoryLoader;
import de.codecentric.jbehave.junit.monitoring.JUnitReportingRunner;

@RunWith(JUnitReportingRunner.class)
public class AllStoriesTest extends JUnitStories {

    private final CrossReference xref = new CrossReference();

    public AllStoriesTest() {
        configuredEmbedder()//
                .embedderControls()//
                .doGenerateViewAfterStories(true)//
                .doIgnoreFailureInStories(false)//
                .doIgnoreFailureInView(true)//
                .doVerboseFailures(true)//
                .useThreads(2)//
                .useStoryTimeoutInSecs(60);
    }

    @Override
    public Configuration configuration() {
        Class&lt;? extends Embeddable&gt; embeddableClass = this.getClass();
        URL codeLocation = codeLocationFromClass(embeddableClass);
        StoryReporterBuilder storyReporter = //
        new StoryReporterBuilder() //
                .withCodeLocation(codeLocation) //
                .withDefaultFormats() //
                .withFormats(CONSOLE, //
                        HTML_TEMPLATE) //
                .withFailureTrace(true) //
                .withFailureTraceCompression(true) //
                .withCrossReference(xref)
                ;
        return new MostUsefulConfiguration() //
                .useStoryLoader(new UTF8StoryLoader(embeddableClass)) //
                .useStoryReporterBuilder(storyReporter) //
                .useStepMonitor(xref.getStepMonitor())//
                ;
    }

    @Override
    protected List&lt;String&gt; storyPaths() {
        URL searchInURL = codeLocationFromClass(this.getClass());
        return new StoryFinder().findPaths(searchInURL, &quot;**/*.story&quot;, &quot;&quot;);
    }

    @Override
    public InjectableStepsFactory stepsFactory() {
        return new SpringStepsFactory(configuration(),
                Springs.createAnnotatedContextFromBasePackages(&quot;bdd101&quot;));
    }
}
[/java]

<section>
<p style="text-align: justify">Quelques explications:</p>

<ul style="text-align: justify">
    <li>L'annotation <code>@RunWith(JUnitReportingRunner.class)</code> indique à <em>JUnit</em> le lanceur qui doit être utilisé pour exécuter notre test.</li>
    <li>Le nom de notre classe finit par <code>Test</code> afin de suivre les conventions usuelles et étend la classe JBehave: <code>JUnitStories</code> afin de faciliter l'intégration JUnit/JBehave.</li>
    <li>Notre constructeur définit l'<code>Embedder</code> JBehave (c'est à dire l'environnement global d'exécution des tests JBehave) qui sera utilisé. Nous verrons les options activées au fur et à mesure de notre article. Ce qu'il faut retenir, c'est que <strong>ces paramètres permettent de contrôler l'exécution des tests</strong> (<code>useStoryTimeoutInSecs</code>, <code>useThreads</code>) et la perception globale des tests (<code>doVerboseFailures</code>) : un test en échec arrête-t-il l'exécution (<code>doIgnoreFailureInStories</code>) ou est-ce lors de la génération du rapport consolidé (<code>doGenerateViewAfterStories</code>) que l'on considérera que l'exécution est en échec (<code>doIgnoreFailureInView</code>) ?
Chaque test <em>JBehave</em> étant lancé de manière indépendante, à la fin de chaque test, <em>JBehave</em> consolide les résultats dans un unique rapport.</li>
    <li>Vient ensuite la seconde partie de la configuration de notre environnement d'exécution. On retiendra pour le moment deux paramètres importants:
<ul>
    <li>les types de rapport qui seront générés, avec notamment la sortie <code>CONSOLE</code> qui facilitera la phase de développement dans notre IDE, et la sortie <code>HTML_TEMPLATE</code> que nous verrons plus tard et qui permet d'avoir un joli rapport html.</li>
    <li>L'utilisation d'une classe spéciale <code>UTF8StoryLoader</code> qui nous permettra de nous affranchir des problématiques d'encodage qui peuvent apparaître dans le cas de développement multi-plateforme. On impose ici l'utilisation systématique de l'UTF8, ce qui correspond au choix que nous avons fait dans notre fichier <code>pom.xml</code> de maven.</li>
</ul>
</li>
    <li>On trouve ensuite la méthode permettant de récupérer la liste des fichiers <code>*.story </code>à exécuter. Il y (au moins) deux pièges dans cette déclaration:
<ul>
    <li>Le premier (qui est aussi directement lié à l'utilisation de notre classe <code>UTF8StoryLoader</code>) est que les fichiers seront chargés comme ressources Java, il convient donc d'indiquer des chemins relatifs à notre classpath. Ce qui nous amène au second piège:</li>
    <li>La méthode utilisée ici se base sur l'emplacement de notre classe de test, il est donc important de placer nos fichiers <code>*.story</code> dans les ressources maven correspondantes: <code>src/test/resources</code> (copiées dans <code>target/test-classes</code>) si notre lanceur est dans un package de <code>src/test/java</code>, ou <code>src/main/resources</code> (copiées dans <code>target/classes</code>) si notre lanceur est dans un package de <code>src/main/java</code>.</li>
</ul>
</li>
</ul>
<h2 style="text-align: justify">Notre premier scénario</h2>
<p style="text-align: justify">Commençons simplement par le développement d'une petite calculatrice.</p>
<p style="text-align: justify">Écrivons notre premier scénario <code>src/test/resources/stories/calculator.story</code>:</p>

</section>

[text]
Scenario: 2+2

Given a variable x with value 2
When I add 2 to x
Then x should equal to 4
[/text]

<section>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/000-calculator-no-step-def.png" alt="Editeur Eclipse de scenario" /></p>
<p style="text-align: justify"><img style="float: left;margin: 5px" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-astuce.png" alt="Astuce du plugin JBehave" width="110px" /> On peux constater que toutes nos étapes sont soulignées en rouge pour indiquer que notre éditeur n'est pas parvenu à les associer au code java correspondant.</p>
<p style="text-align: justify">Exécutons notre lanceur de scénario: <code>Run as / JUnit Test</code> sur la classe <code>AllStoriesTest</code>.</p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/000-junit-runner.png" alt="Vue JUnit Eclipse" /></p>
<p style="text-align: justify">La console Eclipse (Rappel: la sortie console est activée grâce à l'option <code>CONSOLE</code>) affiche alors la sortie suivante:</p>
</section>

[text]
(stories/calculator.story)
Scenario: 2+2
Given a variable x with value 2 (PENDING)
When I add 2 to x (PENDING)
Then x should equal to 4 (PENDING)

@Given(&quot;a variable x with value 2&quot;)
@Pending
public void givenAVariableXWithValue2() {
  // PENDING
}

@When(&quot;I add 2 to x&quot;)
@Pending
public void whenIAdd2ToX() {
  // PENDING
}

@Then(&quot;x should equal to 4&quot;)
@Pending
public void thenXShouldEqual4() {
  // PENDING
}
[/text]

<section>
<p style="text-align: justify">Faisons un petit point du résultat obtenu et qui peut être confus au premier abord:</p>

<ul style="text-align: justify">
    <li>Notre test <em>JUnit</em> est vert ! Ce qui est déroutant !</li>
    <li>Grâce au lanceur <em>JUnit</em>
(<code>de.codecentric.jbehave.junit.monitoring.JUnitReportingRunner</code>) la vue <em>JUnit</em> nous affiche l'intégralité des étapes qui ont été jouées: <code>BeforeStories</code>, notre scénario et les étapes <code>AfterStories.</code></li>
    <li>Toutes les étapes de notre scénario sont marquées <code>PENDING</code> et ont été ignorées lors de l'exécution du test.</li>
    <li><code>PENDING</code> signifie que les étapes présentes dans notre fichier <code>story</code> n'ont pas leurs correspondants dans le code <em>Java</em>, où les méthodes qui doivent être invoquées sont annotées avec le texte du step correspondant. C'est ce qui est d'ailleurs proposé par <em>JBehave</em> en suggestion d'implémentation dans la console. Pour mettre en échec les étapes <code>PENDING</code> et donc pour que notre test ne soit plus vert, il suffit de changer la stratégie par défaut dans la classe <code>AllStoriesTest</code> par <code>FailingUponPendingStep</code>:</li>
</ul>
</section>

[java]
  ...
  return new MostUsefulConfiguration() //
                .useStoryLoader(new UTF8StoryLoader(embeddableClass)) //
                .useStoryReporterBuilder(storyReporter) //
                .usePendingStepStrategy(new FailingUponPendingStep())
                .useStepMonitor(xref.getStepMonitor())//
                ;
[/java]

<section>

<section>
<p style="text-align: justify">Créons donc une classe <code>bdd101.calculator.CalculatorSteps</code> qui contiendra nos premières définitions d'étapes (Steps) basées en partie sur les propositions faites par <em>JBehave</em> dans la console:</p>
</section>[java]
import org.jbehave.core.annotations.Given;
import org.jbehave.core.annotations.Named;
import org.jbehave.core.annotations.Then;
import org.jbehave.core.annotations.When;

import bdd101.util.StepsDefinition;

@StepsDefinition
public class CalculatorSteps {

    @Given(&quot;a variable $variable with value $value&quot;)
    public void defineNamedVariableWithValue(String variable, int value) {
        throw new UnsupportedOperationException();
    }

    @When(&quot;I add $value to $variable&quot;)
    public void addValueToVariable(@Named(&quot;variable&quot;) String variable, 
                                   @Named(&quot;value&quot;)int value) {
        throw new UnsupportedOperationException();
    }

    @Then(&quot;$variable should equal to $expected&quot;)
    public void assertVariableEqualTo(String variable, int expectedValue) {
        throw new UnsupportedOperationException();
    }
}
[/java]
<section>
<p style="text-align: justify">Relisons cette classe ligne par ligne:</p>

<ul style="text-align: justify">
    <li><code>@StepsDefinition</code> est une annotation personnelle qui permet à la fois de marquer cette classe comme contenant des définitions d'étapes (ce qui est purement informatif) et qui <strong>permet à <em>Spring</em> de la détecter au moment où il va parcourir les classes pour la construction de son contexte</strong>; pour plus d'informations, voir la documentation de <em>Spring</em> sur l'utilisation des annotations (<a href="http://static.springsource.org/spring/docs/3.0.0.M3/spring-framework-reference/html/ch04s12.html#beans-scanning-filters">Spring - Using filters to customize scanning</a>).</li>
</ul>
</section>[java]
import java.lang.annotation.Documented;
import org.springframework.stereotype.Component;

@Documented
@Component
public @interface StepsDefinition {}
[/java]

<section>
<ul style="text-align: justify">
    <li>Les étapes sont définies grâce à des annotations spécifiques: <code>@Given</code>, <code>@When</code> et <code>@Then</code>.</li>
    <li>La valeur de chaque annotation correspond à la phrase dans le scénario. Nous avons gardé la configuration par défaut qui spécifie que dans ces phrases, les mots commençant par <code>$ </code>désignent les variables. Ainsi, la première annotation permet de supporter les phrases suivantes:
<ul>
    <li><code>Given a variable x with value 2</code></li>
    <li><code>Given a variable y with value 17</code></li>
    <li>...</li>
</ul>
</li>
    <li>Les variables sont passées en paramètre dans le même ordre qu'elles apparaissent dans la phrase. Si cet ordre n'est pas satisfaisant, il est possible d'annoter chaque paramètre, <code>@Named</code>, pour indiquer la variable qu'il référence (Lignes 17 et 18).</li>
    <li>La conversion d'une variable dans le type du paramètre se fait automatiquement à l'aide des converteurs prédéfinis. Il est possible d'ajouter de nouveaux converteurs.</li>
    <li>Toutes nos étapes génèrent une exception dans notre implémentation initiale.</li>
</ul>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/001-calculator-unsupported-step-def.png" alt="Editeur Eclipse de scenario" /></p>
<p style="text-align: justify"><img style="float: left;margin: 5px" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-astuce.png" alt="Astuce du plugin JBehave" width="110px" /> On peux constater qu'une fois ces étapes enregistrées, notre éditeur de scénario nous indique que toutes nos étapes sont bien définies. Les variables apparaissent avec une couleur différente mettant en évidence leurs emplacements.</p>
<p style="text-align: justify">Exécutons à nouveau notre test:</p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/001-junit-runner.png" alt="Vue JUnit Eclipse" /></p>
</section>

[text]
(stories/calculator.story)
Scenario: 2+2
Given a variable x with value 2 (FAILED)
(java.lang.UnsupportedOperationException)
When I add 2 to x (NOT PERFORMED)
Then x should equal to 4 (NOT PERFORMED)

java.lang.UnsupportedOperationException
    at bdd101.calculator.CalculatorSteps.defineNamedVariableWithValue(CalculatorSteps.java:14)
    (reflection-invoke)
[/text]

<section>
<p style="text-align: justify">On constate désormais que notre test est en échec, que seule la première étape à été exécutée mais qu'elle a échoué <code>FAILED</code> en générant une exception, ce qui correspond bien à notre implémentation. La suite du scénario n'a pas été exécutée: <code>NOT PERFORMED</code></p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/bdd-cycle-around-tdd-cycles.png" alt="Cycle BDD et Cycles TDD" width="400px" /></p>
<p style="text-align: justify">Passons rapidement sur le développement de notre calculatrice (par une approche de type TDD par exemple) pour arriver à une implémentation fonctionnelle (au sens "qui fonctionne"...). Nous obtenons alors la classe <code>Calculator</code> suivante:</p>
</section>[java]
import java.util.HashMap;
import java.util.Map;

public class Calculator {
    private final Map&lt;String, Integer&gt; context;

    public Calculator () {
      context = new HashMap&lt;String, Integer&gt;();
    }

    public void defineVariable(String variable, int value) {
        context.put(variable, value);
    }
    
    public void addToVariable(String variable, int value) {
        int existing = getVariableValueOrFail(variable);
        context.put(variable, value + existing);
    }
    
    public int getVariableValue(String variable) {
        return getVariableValueOrFail(variable);
    }

    protected int getVariableValueOrFail(String variable) {
        Integer existing = context.get(variable);
        if(existing==null)
            throw new IllegalStateException(
              &quot;Variable &lt;&quot; + variable + &quot;&gt; is not defined&quot;);
        return existing;
    }
}
[/java]

<section>
<p style="text-align: justify">Il est désormais nécessaire de faire le lien entre notre calculateur (<code>Calculator</code>) et la définition de nos étapes (<code>CalculatorSteps</code>).</p>
<p style="text-align: justify"><img style="float: left;margin: 5px" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-astuce.png" alt="Astuce du plugin JBehave" width="110px" /> Dans notre éditeur de scénario, il est possible d'accéder directement à la méthode correspondante soit par Ctrl+Clic sur l'étape concernée soit en ayant le curseur sur la ligne correspondante et en appuyant sur Ctrl+G (GO!).</p>
</section>
[java]
public class CalculatorSteps {
    private Calculator calculator = new Calculator ();

    @Given(&quot;a variable $variable with value $value&quot;)
    public void defineNamedVariableWithValue(String variable, int value) {
        calculator.defineVariable(variable, value);
    }

    ...
}
[/java]

<section>
<p style="text-align: justify">En relançant notre test, nous obtenons cette fois:</p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/002-junit-runner.png" alt="Vue JUnit Eclipse" /></p>
<p style="text-align: justify">Bon ! A ce stade, vous devriez avoir un bon aperçu du fonctionnement, faisons un petit saut dans le temps pour arriver à l'implémentation finale de nos étapes:</p>
</section>[java]
...

@When(&quot;I add $value to $variable&quot;)
public void addValueToVariable(@Named(&quot;variable&quot;) String variable, 
                               @Named(&quot;value&quot;)int value) {
    calculator.addToVariable(variable, value);
}

@Then(&quot;$variable should equal to $expected&quot;)
public void assertVariableEqualTo(String variable, int expectedValue) {
    assertThat(calculator.getVariableValue(variable), equalTo(expectedValue));
}
[/java]
<section>
<p style="text-align: justify">Avant de faire un petit point avec notre client, enrichissons un peu notre histoire en lui ajoutant de nouveaux scénarios.</p>
<p style="text-align: justify">On commencera par un petit copier/coller (et oui, on a le droit !) pour vérifier que l'on peut utiliser d'autres noms de variables et d'autres valeurs que 2. Et même que l'on peut mixer l'utilisation de plusieurs variables.</p>
</section>

[text]
Scenario: 2+2 avec une variable y

Given a variable y with value 2
When I add 2 to y
Then y should equal to 4

Scenario: 37+5 avec une variable UnBienJoli_Nom

Given a variable UnBienJoli_Nom with value 37
When I add 5 to UnBienJoli_Nom
Then UnBienJoli_Nom should equal to 42

Scenario: 7+2 et 9+4 avec une variable y et une variable x

Given a variable y with value 7
Given a variable x with value 9
When I add 2 to y
When I add 4 to x
Then x should equal to 13
Then y should equal to 9
[/text]

<section>
<p style="text-align: justify">Hummm et si on utilise une variable qui n'existe pas ? Eh bien la réponse est à voir avec le client! Faisons un petit point avec notre client. Il nous dit que ça serait bien si on pouvait faire plusieurs additions sur la même variable.</p>
</section>
[text]
Scenario: 37+5+6+17 

Given a variable x with value 37
When I add 5 to y
And I add 6 to x
And I add 17 to x
Then x should equal to 65
[/text]

<section>
<p style="text-align: justify"><img style="float: left;margin: 5px" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-astuce.png" alt="Astuce du plugin JBehave" width="110px" /> Dans notre éditeur de scénario, il est possible d'obtenir une complétion automatique parmi les étapes disponibles par <code>Ctrl+Espace</code>. Il est aussi possible de faire une recherche parmi toutes les étapes disponibles en pressant <code>Ctrl+J</code></p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-quick-search-001.png" alt="Plugin JBehave recherche rapide" /></p>
<p style="text-align: justify"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/plugin-quick-search-002-filter.png" alt="Plugin JBehave recherche rapide avec filtre" /></p>
<p style="text-align: justify">Relançons notre test:</p>
</section>

[text]
Scenario: 37+5+6+17
Given a variable x with value 37
When I add 5 to y
And I add 6 to x
And I add 17 to x
Then x should equal to 65 (FAILED)
(java.lang.AssertionError: 
Expected: &lt;65&gt;
     got: &lt;60&gt;
)
[/text]

<section>
<p style="text-align: justify">Humpf ! ça, c'était pas prévu ! Que s'est-il passé ? En regardant de plus près, on peut voir que l'on s'est trompé ligne 4, on ajoute 5 à la variable <code>y</code> au lieu de la variable <code>x</code>...
Ce qui soulève deux problèmes: comment se fait-il que la variable <code>y</code> existe et pourquoi n'a-t-on pas eu d'erreur ? Ce qui nous permet au passage de voir avec notre client comment il souhaite prendre en compte l'utilisation de variable non définie. Ensemble, nous définissons alors un nouveau scénario:</p>
</section>

[text]
Scenario: Undefined variable displays error message

When I add 5 to y
Then the calculator should display the message 'Variable &lt;y&gt; is not defined'
[/text]

<section>
<p style="text-align: justify">Maintenant, intéressons-nous à notre erreur précédente: comment se fait-il que nous n'ayons pas eu d'erreur (<code>IllegalStateException)?</code> Et bien, tout simplement parce que l'un des scénarios précédent a défini cette variable, et que les classes définissant les étapes ne sont pas réinstanciées à chaque test: nous utilisons donc la même instance de <code>Calculator</code> pour tous les scénarios.</p>
<p style="text-align: justify"><strong>Les classes définissant les étapes ne sont instanciées qu'une seule fois pour tous les fichiers <code>*.story</code> et pour tous les scénarios d'un fichier <code>story</code></strong>. Et même de manière concurrente si l'on spécifie que les scénarios peuvent être exécutés à travers plusieurs <code>Thread</code>.</p>
<p style="text-align: justify">Cela nous amène à présenter <strong>quelques bonnes pratiques</strong>:</p>

<ul style="text-align: justify">
    <li><strong>ne pas stocker d'états dans les classes définissant les étapes</strong></li>
    <li>utiliser les annotations <code>@BeforeStories</code>, <code>@BeforeStory</code>, <code>@BeforeScenario</code> pour réinitialiser les états entre chaque scénario. Dans le cas de tests unitaires, on pourra se contenter de réinitialiser uniquement le contexte du test avant chaque scénario <code>@BeforeScenario</code>. Tandis que dans le cas des tests d'intégration, on pourra par exemple démarrer le serveur Sélenium, ou le serveur d'application, au tout début des tests dans une méthode annotée <code>@BeforeStories</code>, réinitialiser la base de données avant chaque histoire <code>@BeforeStory</code> et réinitialiser le contexte du test avant chaque scénario <code>@BeforeScenario</code>.</li>
    <li>utiliser les annotations <code>@AfterStories</code>, <code>@AfterStory</code> et <code>@AfterScenario</code> pour fermer et nettoyer les ressources correspondantes.</li>
</ul>
<p style="text-align: justify">Afin de garder une infrastructure de test simple qui nous permettra de travailler en environnement concurrent, nous opterons pour l'utilisation de variable <code>ThreadLocal</code> pour maintenir l'état de chaque scénario. Ainsi, deux scénarios s'exécutant en parallèle (chacun dans leur thread) disposeront chacun de leur propre contexte.</p>
</section>

[java]
public class CalculatorContext {

    private static ThreadLocal&lt;CalculatorContext&gt; threadContext = 
            new ThreadLocal&lt;CalculatorContext&gt;();
    
    public static CalculatorContext context() {
        return threadContext.get();
    }
    
    public static Calculator calculator() {
        return context().getCalculator();
    }
    
    public static void initialize() {
        // one does not rely on ThreadLocal#initialValue()
        // so that one is sure only initialize create a new
        // instance
        threadContext.set(new CalculatorContext());
    }
    public static void dispose () {
        threadContext.remove();
    }
    
    private final Calculator calculator;
    private Exception lastError;
    
    public CalculatorContext() {
        calculator = new Calculator();
    }
    
    public Calculator getCalculator() {
        return calculator;
    }
    
    public void setLastError(Exception lastError) {
        this.lastError = lastError;
    }
    
    public Exception getLastError() {
        return lastError;
    }
}
[/java]

<section>
<p style="text-align: justify">Modifions enfin notre classe <code>CalculatorSteps</code>:</p>
</section>

[java]
package bdd101.calculator;

import static bdd101.calculator.CalculatorContext.calculator;
import static org.hamcrest.CoreMatchers.equalTo;
import static org.hamcrest.MatcherAssert.assertThat;

import org.jbehave.core.annotations.AfterScenario;
import org.jbehave.core.annotations.BeforeScenario;
import org.jbehave.core.annotations.Given;
import org.jbehave.core.annotations.Named;
import org.jbehave.core.annotations.Then;
import org.jbehave.core.annotations.When;

import bdd101.util.StepsDefinition;

@StepsDefinition
public class CalculatorSteps {
    
    @BeforeScenario
    public void inializeScenario() {
        CalculatorContext.initialize();
    }

    @AfterScenario
    public void disposeScenario() {
        CalculatorContext.dispose();
    }
    
    @Given(&quot;a variable $variable with value $value&quot;)
    public void defineNamedVariableWithValue(String variable, int value) {
        calculator().defineVariable(variable, value);
    }

    @When(&quot;I add $value to $variable&quot;)
    public void addValueToVariable(@Named(&quot;variable&quot;) String variable, 
                                   @Named(&quot;value&quot;)int value) {
        calculator().addToVariable(variable, value);
    }
}
[/java]

<section>
<p style="text-align: justify">Relançons les tests, et cette fois nous obtenons bien l'exception souhaitée:</p>
</section>
[text]
...

Scenario: Undefined variable displays error message
When I add 5 to y (FAILED)
(java.lang.IllegalStateException: Variable &lt;y&gt; is not defined)
Then the calculator should display the message 'Variable y is not defined' (PENDING)
@Then(&quot;the calculator should display the message 'Variable y is not defined'&quot;)
@Pending
public void thenTheCalculatorShouldDisplayTheMessageVariableYIsNotDefined() {
  // PENDING
}
[/text]

<section>
<p style="text-align: justify">Modifions légèrement notre classe de définitions d'étapes pour gérer l'exception:</p>
</section>
[java]
...
    @When(&quot;I add $value to $variable&quot;)
    public void addValueToVariable(@Named(&quot;variable&quot;) String variable, 
                                   @Named(&quot;value&quot;)int value) {
        try {
            calculator().addToVariable(variable, value);
        } catch (Exception e) {
            context().setLastError(e);
        }
    }
...
[/java]

<section>
<p style="text-align: justify">L'erreur pouvant être de nature "métier" (le code est ici simplifié), ce n'est généralement pas à une étape de type <code>Given</code> ou <code>When</code> <span style="color: #333333">de la traiter</span>. Les assertions devraient autant que possible se situer dans les méthodes <code>Then</code>.</p>
<p style="text-align: justify">Puis enfin, ajoutons l'étape de vérification:</p>
</section>

[java]
...

@Then(&quot;the calculator should display the message '$errorMessage'&quot;)
public void assertErrorMessageIsDisplayed(String errorMessage) {
  Exception lastError = context().getLastError();
  assertThat(&quot;Not in error situtation&quot;, lastError, notNullValue());
  assertThat(&quot;Wrong error message&quot;, lastError.getMessage(), equalTo(errorMessage));
}
[/java]

<section>
<p style="text-align: justify">Afin de s'assurer que tous nos scénarios précédents restent cohérents, nous ajoutons aussi l'étape suivante <code>the calculator should not be in error</code> à la fin de chaque scénario.</p>
</section>

[text]
Scenario: 2+2

Given a variable x with value 2
When I add 2 to x
Then x should equal to 4
And the calculator should not be in error

Scenario: 2+2 avec une variable y

Given a variable y with value 2
When I add 2 to y
Then y should equal to 4
And the calculator should not be in error

...
[/text]

<section>
<p style="text-align: justify">La méthode correspondante à l'étape:</p>
</section>

[java]
...
@Then(&quot;the calculator should not be in error&quot;)
public void assertNoErrorMessageIsDisplayed() {
  Exception lastError = context().getLastError();
  assertThat(lastError, nullValue());
}
[/java]

<h1 style="text-align: justify">Conclusion</h1>
<p style="text-align: justify">Un schéma vaut mieux qu'un long discours:</p>
<p style="text-align: justify"><a href="http://www.modernanalyst.com/Resources/BusinessAnalystHumor/tabid/218/articleType/ArticleView/articleId/1231/Theres_an_app_for_that.aspx"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/Fin50s.jpg" alt="There's an app for that!" /></a></p>
<p style="text-align: justify">En attendant le "Specs Creator", le BDD est une bonne alternative !</p>

<h1 style="text-align: justify">Références et Liens</h1>
<p style="text-align: justify">Le code complet est disponible ici: <a href="https://github.com/Arnauld/jbehave-get-started">jbehave-get-started</a>.</p>
<p style="text-align: justify">Articles:</p>

<ul style="text-align: justify">
    <li><a href="http://dannorth.net/introducing-bdd/">Dan North - Introduction to BDD</a> (traduction française par <a href="http://philippe.poumaroux.free.fr/index.php?post/2012/02/06/Introduction-au-Behaviour-Driven-Developement">Philippe Poumaroux</a>)</li>
    <li><a href="http://dannorth.net/2012/05/31/bdd-is-like-tdd-if/">Dan North - BDD is like TDD if...</a></li>
    <li><a href="http://herdingcode.com/?p=176">Herding Code #42 ~ Scott Bellware on BDD</a></li>
    <li><a href="http://lizkeogh.com/2009/11/06/translating-tdd-to-bdd/">Liz Keogh - Translating TDD to BDD</a></li>
    <li><a href="http://specificationbyexample.com/">Gojko Adzic - Specification by Example: How successful teams deliver the right software</a></li>
</ul>
<p style="text-align: justify">Outils:</p>

<ul>
    <li><a href="http://jbehave.org/">JBehave</a></li>
    <li><a href="http://github.com/codecentric/jbehave-junit-runner">Code Centric ~ jbehave-junit-runner</a></li>
    <li><a href="http://github.com/Arnauld/jbehave-eclipse-plugin">JBehave Eclipse plugin</a></li>
</ul>
</section>
