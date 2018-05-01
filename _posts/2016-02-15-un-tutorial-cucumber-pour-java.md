---
layout: post
title: Un tutorial Cucumber pour Java
date: 2016-02-15 15:08
author: ronan le roy
comments: true
categories: [bdd, Bonnes pratiques de dév, cucumber, Outils, Programmation, software craftsmanship, TDD]
---
<h2>Pourquoi</h2>

Au début des années 2000, avant la généralisation des usines logicielles et la simplification des frameworks de test, je trouvais que faire des tests, c'était compliqué.
Du coup, je n'écrivais pas de tests automatisés. 
En revanche, j'ai également toujours ressenti le besoin de pouvoir lancer des fractions ou la totalité de mon code dans des conditions propices à sa vérification.
Pour en consulter le résultat, ou en pas à pas, afin de regarder la valeur de différentes variables au milieu d'une exécution. 
Du coup je rajoutais des méthodes "main" un peu partout dans mes classes.

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/caveman.jpg" alt="Image" />

C'était pratique, simple à mettre en place, efficace pour mes développements, un premier pas vers le TDD.

[code lang=text]
public class MyService {

    public boolean doSomething(){
        [...]
    }

    public static void main(String[] args) {
        MyService service = new Myservice();
        boolean success = service.doSomething();
        if (!success) {
            throw new RuntimeException(&quot;Houston, we have a problem !&quot;)
        }
    }
}
[/code]

<a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v1main/MyServiceTest.java">MyServiceTest.java</a>

<h2>Simple comme TDD</h2>

Et puis on m'a montré JUnit, et que c'était quand même tout simple comme framework de tests.
Plutôt que d'écrire un "main", je pouvais appeler ma méthode comme je voulais, en avoir plusieurs dans la même classe.
Il suffisait d'ajouter à ladite méthode une simple annotation et le tour était joué. 
Et en plus, ces tests étaient détectés automatiquement par l'usine logicielle et la chaîne d'intégration continue.
Très bien pour faire du TDD !

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/simple.jpg" alt="Image" />

Dans le <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/pom.xml">pom.xml</a> :

[code lang=text]
&lt;dependency&gt;
    &lt;groupId&gt;junit&lt;/groupId&gt;
    &lt;artifactId&gt;junit&lt;/artifactId&gt;
    &lt;version&gt;4.12&lt;/version&gt;
&lt;/dependency&gt;
[/code]

Dans <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/main/java/com/leroy/ronan/tuto/cucumber/services/MyService.java">src/main/java</a>

[code lang=text]
public class MyService {

    public boolean doSomething(){
        [...]
    }
}
[/code]

Dans <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v2junit/MyServiceTest.java">src/test/java</a>

[code lang=text]
public class MyServiceTest {

    @Test
    public void doSomething(){
        MyService service = new Myservice();
        boolean success = service.doSomething();
        Assert.assertTrue(&quot;Houston, we have a problem !&quot;, success);
    }
}
[/code]

Jusqu'ici, à part raconter une histoire, je ne pense pas vous avoir appris grand chose.
Mais je me suis assuré que les bases étaient en place. Maintenant, passons aux choses sérieuses !

<h2>Le Concombre et le Cornichon</h2>

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/cucumber.jpg" alt="Image" />

Ne vous inquiétez pas, je ne vais pas vous conter une fable qui aurait tout à envier à La Fontaine 
mais vous montrer qu'il est extrêmement simple d'aller plus loin que JUnit pour vos tests automatisés
et de les écrire en "langue naturelle".
Par défaut, cette langue sera l'anglais, mais il est possible très simplement d'en utiliser une autre. 
Il faudra suivre une certaine syntaxe, celle du Gherkin. 
Et JUnit déclenchera l'exécution de Cucumber.

Commencer par ajouter dans le <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/pom.xml">pom.xml</a> :

[code lang=text]
    &lt;dependency&gt;
        &lt;groupId&gt;info.cukes&lt;/groupId&gt;
        &lt;artifactId&gt;cucumber-java&lt;/artifactId&gt;
        &lt;version&gt;1.2.4&lt;/version&gt;
        &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;info.cukes&lt;/groupId&gt;
        &lt;artifactId&gt;cucumber-junit&lt;/artifactId&gt;
        &lt;version&gt;1.2.4&lt;/version&gt;
        &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
[/code]

Ensuite, parce que j'aime bien avoir dans le même dossier les fichiers Gherkin et les fichiers Java s'y rapportant, 
il faut l'indiquer à Maven (j'entends d'ici vos hurlements, c'est une préférence personnelle, libre à vous de laisser ces fichiers dans le dossier "resources").

[code lang=text]
    &lt;build&gt;
        &lt;testResources&gt;
            &lt;testResource&gt;
                &lt;directory&gt;src/test/resources&lt;/directory&gt;
            &lt;/testResource&gt;
            &lt;testResource&gt;
                &lt;directory&gt;src/test/java&lt;/directory&gt;
                &lt;includes&gt;
                    &lt;include&gt;**/*.feature&lt;/include&gt;
                &lt;/includes&gt;
            &lt;/testResource&gt;
        &lt;/testResources&gt;
    &lt;/build&gt;
[/code]

Indiquer à JUnit que vous voulez que votre <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v3gherkinsimple/MyServiceTest.java">classe de test</a> tourne avec Cucumber :

[code lang=text]
@RunWith(Cucumber.class)
public class MyServiceTest {

}
[/code]

Créer le fichier feature, <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v3gherkinsimple/my-service.feature">my-service.feature</a> qui contiendra les scenarii de test :

[code lang=text]
Feature: Features of my service

Scenario: Calling my service
Given my service exists
 When I call my service
 Then it should have been a success
[/code]

Executer le test.

Cucumber vous demande alors d'implémenter les phrases présentes dans le scenario.
Il faut les mettre dans une <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v3gherkinsimple/MyServiceSteps.java">nouvelle classe</a> :

[code lang=text]
public class MyServiceSteps {

    private MyService service;
    private boolean success;

    @Given(&quot;^my service exists$&quot;)
    public void my_service_exists() throws Throwable {
        service = new MyService();
    }

    @When(&quot;^I call my service$&quot;)
    public void i_call_my_service() throws Throwable {
        success = service.doSomething();
    }

    @Then(&quot;^it should have been a success$&quot;)
    public void it_should_have_been_a_success() throws Throwable {
        Assert.assertTrue(success);
    }
}
[/code]

Et le tour est joué !

Il est possible de configurer l'exécution de cucumber en ajoutant l'annotation @CucumberOptions. 
L'une d'elle, "strict", permet d'avoir un test en failure si des steps restent à implémenter.
Les autres options permettent d'indiquer l'emplacement des fichiers features ou des classes Java contenant nos annotations @Given, @When, @Then, etc., si ceux-ci ne sont pas dans le même package ou un sous-package du lanceur.

<h2>La syntaxe du Gherkin</h2>

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/gherkin.jpg" alt="Image" />

Et là, vous me direz que par rapport à l'utilisation de JUnit seul, on a plus perdu que gagné :
- on est maintenant sur 2 fichiers au lieu d'un seul. 
- le code Java présente un code éclaté dans différentes méthodes d'une classe.

L’intérêt est que ce nouveau fichier, rédigé en Gherkin, est "lisible".
Il est rédigé en anglais (il est possible de le rédiger dans d'autres langues) et peut être partagé entre les différents acteurs du projet.
Le Gherkin et Cucumber sont des outils aidant à la mise en place des méthodes BDD.
Même si vos collaborateurs n'ont pas connaissance du BDD, le fait de baser votre développement en TDD sur des scénarii en Gherkin vous permettra de les partager et de les faire valider par vos collaborateurs fonctionnels.

Vous pourrez ainsi les faire basculer progressivement vers un fonctionnement BDD où ces scénarii seront écrits en collaboration avec eux.

Voici un rapide aperçu de la syntaxe du Gherkin, le pont entre la langue naturelle et le code.

Le fichier <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v4gherkinsyntax/my-service.feature">my-service.feature</a> contient des exemples de scénarii que je vais détailler ici.
L'implémentation associée est disponible dans <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v4gherkinsyntax/MyServiceSteps.java">MyServiceSteps.java</a>.

<ul>
<li>Le titre :</li>
</ul>

[code lang=text]
Feature: &lt;Titre de la fonctionnalité&gt;
[/code]

<ul>
<li>Un descriptif :</li>
</ul>

Toute la partie avant le "Background", "Scenario" ou "Scenario Outline" est un descriptif libre.

Ne pas hésiter à y résumer la spécification ou à y noter toutes les informations utiles à la fonctionnalité.

<ul>
<li>Un éventuel Background :</li>
</ul>

[code lang=text]
Background: 
[...etapes...]
[/code]

Les "étapes" qui suivront ce Background seront exécutées en préalable à tous les "Scenario" et "Scenario Outline" de ce fichier.

C'est un peu l'annotation "@Before" de JUnit (à noter que les annotations @Before et @After existent également dans Cucumber, elles sont appelées Hook mais je ne vais pas en développer l'utilisation ici).

<ul>
<li>Des scénarii :</li>
</ul>

[code lang=text]
Scenario: &lt;Titre du scénario&gt;
[...etapes...]
[/code]

<ul>
<li>Des templates de scénarii :</li>
</ul>

[code lang=text]
Scenario Outline: &lt;Titre du scénario&gt;
[...etapes...]
Examples:
[...table...]
[/code]

Il se rédige comme un scénario classique, à ceci prêt que celui-ci sera multiplié pour chaque ligne de la table d'exemples.

Dans les étapes, les valeurs entre "" seront remplacées par les valeurs présentes dans les lignes de la table d'exemples.

<ul>
<li>Des étapes :</li>
</ul>

Les étapes sont des phrases commençant par les mots "Given", "When", "Then", "And" ou "But".

A noter que les mots clés "And" et "But" reprennent simplement le mot-clé précédent. Ils permettent d'alléger la lecture du scénario, de mettre de l'emphase comme on le ferait en langue naturelle mais n'ont pas d'effet au niveau de l'implémentation du scénario.

<ul>
<li>Des paramètres dans les étapes</li>
</ul>

Si dans les phrases des étapes, Cucumber détecte un nombre, il le considèrera comme un paramètre de type int.

[code lang=text]
    Then the service should answer with 3 elements
[/code]

donnera :

[code lang=text]
    @Then(&quot;^the service should answer with (\d+) elements$&quot;)
    public void the_service_should_answer_with_elements(int expected) throws Throwable {

[/code]

Si dans les phrases des étapes Cucumber détecte un mot entre guillemets, il le considèrera comme un paramètre de type String.

[code lang=text]
 When &quot;jdoe&quot; calls my service, asking the user names
[/code]

donnera :

[code lang=text]
    @When(&quot;^&quot;([^&quot;]*)&quot; calls my service, asking the user names$&quot;)
    public void calls_my_service_asking_the_user_names(String login) throws Throwable {
[/code]

Libre à vous de remplacer la regexp associée à l'annotation du code généré par une regexp plus pertinente.

Par exemple pour gérer un nombre décimal :

[code lang=text]
  And the service should answer in less than 0.010 seconds
[/code]

pourra donner :

[code lang=text]
    @Then(&quot;^the service should answer in less than (\d+\.\d+) seconds$&quot;)
    public void the_service_should_answer_in_less_than_milliseconds(double seconds) throws Throwable {
[/code]

On peut gérer des chaînes de plusieurs lignes ainsi :

[code lang=text]
Given the messages is
  &quot;&quot;&quot;
  Here is a very long messages.
  This message consists of several lines of text
  but is only one parameter
  &quot;&quot;&quot;
[/code]

Qui devient tout simplement :

[code lang=text]
    @Given(&quot;^the messages is$&quot;)
    public void the_messages_is(String message) throws Throwable {
[/code]

Il est également possible d'avoir des paramètres de type listes :

[code lang=text]
  And the response should be &quot;Roe, Doe, Smith&quot;
[/code]

se traduira par :

[code lang=text]
    @Then(&quot;^the response should be &quot;([^&quot;]*)&quot;$&quot;)
    public void the_response_should_be(List&lt;String&gt; expected) throws Throwable {

[/code]

Pour des tables :

[code lang=text]
Given allowed users are :
 | name  | surname | login  |
 | Roe   | Bill    | broe   |
 | Doe   | John    | jdoe   |
 | Smith | Bob     | bsmith |
[/code]

on obtient alors :

[code lang=text]
    @Given(&quot;^allowed users are :$&quot;)
    public void allowed_users_are(List&lt;Map&lt;String, String&gt;&gt; data) throws Throwable {
[/code]

J'encourage également fortement à nommer correctement les paramètres des fonctions.

Pour plus d'informations : <a href="https://cucumber.io/docs/reference">https://cucumber.io/docs/reference</a>

<h2>Le World</h2>

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/world.jpg" alt="Image" />

Maintenant que vous êtes convaincu de l'intérêt du Gherkin et l'utilisez pour rédiger vos tests, vous allez être confrontés à un nouveau problème :

Votre classe de "Steps" devient obèse, vous aimeriez l'exploser en plusieurs classes, mais vous avez un attribut qui est utilisé dans toutes les étapes.

Pour cela, il existe une solution : le World. Il s'agit d'une classe dont l'instance sera partagée entre toutes vos classes de "Steps" au court d'un scénario.

Pour utiliser un "World", le plus simple est d'intégrer une librairie d'injection de dépendances comme "pico container" qui a la qualité d'être rapide et plus simple que Spring :

[code lang=text]
        &lt;dependency&gt;
            &lt;groupId&gt;info.cukes&lt;/groupId&gt;
            &lt;artifactId&gt;cucumber-picocontainer&lt;/artifactId&gt;
            &lt;version&gt;1.2.4&lt;/version&gt;
            &lt;scope&gt;test&lt;/scope&gt;
        &lt;/dependency&gt;
[/code]

<a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/pom.xml">pom.xml</a>

Vous pourrez ensuite définir un constructeur à vos classes de "Steps" prenant en paramètre ladite classe :

[code lang=text]
public class MyServiceSteps {
    [...]
    private MyServiceWorld world;
    [...]
    public MyServiceSteps(MyServiceWorld world){
        this.world = world;
    }
    [...]
}
[/code]

Toutes vos classes "Steps" utiliseront ainsi la même instance de world et vous pourrez ainsi organiser vos méthodes dans différentes classes.

<a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v5gherkinworld">Cucumber World</a>

<h2>Les tags</h2>

<img src="https://git.framasoft.org/Ronan/tuto-cucumber/raw/master/img/tag.jpg" alt="Image" />

Le Gherkin permet enfin d'ajouter des tags aux scénarii. Ces tags permettront, par exemple, de rédiger une classe de tests qui n'exécutera que les scenarii identifiés par un ou plusieurs tags particuliers. L'identification de scenarii peut se faire au niveau de la feature, ou scénario par scénario.

Des exemples d'utilisations de tags pour piloter les classes de test sont disponibles ici : <a href="https://git.framasoft.org/Ronan/tuto-cucumber/blob/master/src/test/java/com/leroy/ronan/tuto/cucumber/services/v6gherkintags">Cucumber Tags</a>

<ul>
<li>Il suffit d'ajouter @tag une ligne avant le début de la feature ou du scenario.</li>
</ul>

[code lang=text]
        @sample1
        Feature: [...]
        [...]

        @tag
        Scenario: [...]
        [...]
[/code]

Chaque feature ou scenario peut avoir aucun, 1 ou plusieurs tags.

<ul>
<li>Placer chaque tag sur une ligne.</li>
</ul>

[code lang=text]
        @tag1
        @tag2
        Scenario: [...]
        [...]
[/code]

C'est au niveau des options de Cucumber dans la classe de test que l'on indique les tags voulus.
- Le paramètre "tags" prend en entrée une liste de chaîne.

[code lang=text]
        tags = {&quot;@sample1&quot;}
[/code]

<ul>
<li>Chaque chaîne de ce tableau défini une condition qui doit être remplie pour executer le scenario.</li>
</ul>

[code lang=text]
        // sample1 &amp;&amp; sample2
        tags = {&quot;@sample1&quot;, &quot;@sample2&quot;}
[/code]

<ul>
<li>Chaque chaîne de ce tableau doit contenir un ou plusieurs tags séparés par des virgules. La condition sera remplie si le scénario comporte un des tags ainsi précisés.</li>
</ul>

[code lang=text]
        // sample1 || sample2
        tags = {&quot;@sample1,@sample2&quot;}
[/code]

<ul>
<li>Toutes les conditions doivent être remplies pour que le test exécute le scenario.</li>
</ul>

[code lang=text]
        // (sample1 || sample2) &amp;&amp; (sample3 || sample4)
        tags = {&quot;@sample1,@sample2&quot;, &quot;@sample3,@sample4&quot;}
[/code]

<ul>
<li>Si le tag est précédé de '~' c'est qu'il s'agit d'une exclusion.</li>
</ul>

[code lang=text]
        // !ignore
        tags = {&quot;~@ignore&quot;}
[/code]

Il est possible de mettre autant de tags que l'on souhaite (et même plusieurs par lignes). Les tags permettent d'ajouter des informations supplémentaires aux scénarios. Il s'agit de méta-informations ajoutées aux scénarii. Ces informations peuvent être également utilisées pour :

<ul>
<li>marquer le domaine fonctionnel que le scénario couvre, il s'agit alors d'informations purement déclaratives qui peuvent aider à construire une converture fonctionnelle de notre application.</p></li>
<li><p>filtrer l'exécution de certains scénarii, par exemple ne lancer que les scénarii concernant la fonctionnalité "@payment", ou au contraire ne pas lancer les scénarii qui sont en court de développement et qui sont taggés "@wip" comme on vient de le voir.</p></li>
<li><p>déclencher des "Hooks" spécifiques.</p></li>
</ul>

<h2>Vers le BDD</h2>

<p>Attention, Cucumber n'est qu'un outil, certes très utile pour la mise en place des méthodes BDD, mais ce n'est pas parce que vous utilisez Cucumber dans votre projet que vous faites du BDD.

Ce que je me contente d'encourager ici, c'est d'utiliser le Gherkin et Cucumber pour rédiger vos tests quand cela permet de les rendre plus clairs pour vous ou vos relecteurs. Vous pourrez ensuite soumettre vos fichiers Gherkin à vos interlocuteurs métier et ainsi basculer en douceur vers un fonctionnement BDD où les fichiers feature seront rédigés en collaboration.

Il faudra alors bien séparer 2 sortes de fichiers :

<ul>
<li>vos fichiers features, rédigés dans le cadre de votre TDD, plus techniques et se rapportant à des tests unitaires (ceux-ci, je les laisse dans le code).</p></li>
<li><p>les fichiers features du projet, rédigés avec le métier, plus fonctionnels et à considérer comme une spécification (ceux-là, je les garde dans le dossiers "resources").</p></li>
</ul>

<p>A vous de vous organiser comme cela vous convient le mieux, mais si vous pouvez vous permettre de refactorer les premiers, les seconds sont à considérer comme une sorte de contrat à respecter, et vous ne pouvez vous permettre de changer les termes du contrat sans en parler vos interlocuteurs fonctionnels.
