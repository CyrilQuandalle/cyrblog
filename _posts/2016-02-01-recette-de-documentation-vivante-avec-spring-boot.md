---
layout: post
title: Recette de documentation vivante avec Spring Boot (adaptable et gluten free)
date: 2016-02-01 19:00
author: yvan vu
comments: true
categories: [Bonnes pratiques de dév, développement, documentation, java, Outils, Programmation, web]
---
<h1>Entrée.</h1>

Chères lectrices, chers lecteurs, je vais aujourd'hui partager avec vous une recette de ma grand-mère, qui m'a appris à sublimer les rouleaux de printemps au concombre. Pour cela, nous allons avoir besoin des ingrédients suivants :

<ul>
<li>Asciidoctor</li>
<li>Cucumber</li>
<li>SpringBoot (Spring MVC en fait, mais comme est on en pleine hype microservices, faut être SWAG, sinon j'aurais dit JHipster)</li>
</ul>

Mais avant de commencer à trancher dans le lard, intéressons-nous à la base-même du plat du jour, <a href="http://asciidoc.org/" target="_blank"><b>AsciiDoc</b></a> : AsciiDoc est un langage balisé léger du même gabarit que le plus connu <strong>MarkDown</strong>. N'étant un expert ni de l'un ni de l'autre, je ne vais pas créer un débat de goûts et sensations culinaires de chacun d'eux. Sachez juste que selon les auteurs, AsciiDoc a été pensé pour créer toute sorte de document, de la plus simple des notes à (et surtout) une publication entière et professionnelle. Il est complet (c'est à dire sans matière grasse à ajouter) vis à vis de MarkDown et de ce fait, plus complexe. Mais ce qu'il faut retenir avant tout, c'est que l'un ou l'autre vous permettra d'avoir la possibilité de traiter le document que vous écrivez comme du code qui pourrait être versionné : vous pouvez donc utiliser n'importe quel outil de comparaison pour regarder les différences entre 2 versions. Par contre, oubliez les éditeurs WYSIWYG car les langages balisés légers sont faits pour être facilement utilisables et pour se passer des éditeurs de texte comme Word.

Notre ingrédient principal est donc <a href="http://asciidoctor.org/#the-big-picture" target="_blank"><b>AsciiDoctor</b></a>, un outil open-source permettant de transformer des documents AsciiDoc en documents HTML5, PDF, EPUB3 et autres. Il se différencie grâce à son intégration simplifiée à des projets à base de Ruby, Javascript, Java et autres langages basés sur la JVM. Il permet aussi d'étendre le langage AsciiDoc afin de pouvoir récupérer une partie ou l'ensemble d'un fichier tierce, se révélant alors très pratique pour extraire des parties de votre code source. Il peut aussi être étendu afin d'intégrer des graphes ou encore de customiser la sortie afin de créer une présentation slideshow.
Son créateur, Dan Allen, est très actif pour améliorer sans cesse l'outil. Il fait aussi beaucoup de présentations sur ce sujet, notamment aux derniers Devoxx France, Belgique et Maroc.

Lorsque vous faites du BDD, il y aura forcément un moment où vous avez besoin de décrire vos scénarios et allez en profiter pour baser vos tests dessus. Pour son côté rafraichissant et peu calorique, <a href="https://cucumber.io/" target="_blank"><b>Cucumber</b></a> est une solution de choix, avec l'écriture des fameux Given-When-Then en syntaxe <strong>Gherkin</strong>. Ici, nous allons transformer les scénarios et leurs résultats en documentation grâce à <a href="https://github.com/rmpestano/cukedoctor" target="_blank"><b>CukeDoctor</b></a>. CukeDoctor s'appuie sur AsciiDoctor pour transformer les résultats JSON de Cucumber en document HTML5 qui indiqueront, à la manière d'un report, les différentes étapes et le résultat de chaque scénario.

Enfin, pour le dressage et la cuisson, nous allons utiliser Spring Boot et pour documenter son API REST basée sur Spring MVC, nous ajouterons <strong><a href="http://projects.spring.io/spring-restdocs/" target="_blank">Spring REST Docs</a></strong>. Ce projet et plugin maven va vous permettre de faire 2 choses à la fois : tester votre API REST (comme Spring MVC Tests) et en sortir une documentation AsciiDoctor. Chacun de vos tests sur les controllers va générer des mini-documents AsciiDoctor que vous importerez afin de donner des exemples d'appels et d'indiquer les différents paramètres, leur signification et les valeurs qu'elles peuvent prendre. Fini donc le plat préparé <a href="http://petstore.swagger.io/#!/store/getInventory" target="_blank">Swagger</a> du supermarché, place à l'authentique documentation à la manière de <a href="https://developer.github.com/v3/" target="_blank">GitHub</a> !

<h1>Plat principal</h1>

Sortez vos outils, vos tablettes à découper et de quoi noter. Commençons donc par télécharger un moule tout fait d'application maven avec spring boot web (pensez donc à sélectionner le starter Web) sur <a href="https://start.spring.io/" target="_blank">https://start.spring.io/</a>.

<h2>AsciiDoctor</h2>

Nous allons maintenant nous intéresser à écrire une documentation remplaçant les spécifications fonctionnelles et techniques en asciidoctor.
Pour cela, le plus simple est de rajouter un plugin maven au pom.xml (dans la partie build &gt; plugins donc):

[code lang=xml]
&lt;plugin&gt;
    &lt;groupId&gt;org.asciidoctor&lt;/groupId&gt;
    &lt;artifactId&gt;asciidoctor-maven-plugin&lt;/artifactId&gt;
    &lt;version&gt;1.5.3&lt;/version&gt;
    &lt;executions&gt;
        &lt;execution&gt;
            &lt;id&gt;ascidoctor-html5-documentation&lt;/id&gt;
            &lt;phase&gt;generate-resources&lt;/phase&gt;
            &lt;goals&gt;
                &lt;goal&gt;process-asciidoc&lt;/goal&gt;
            &lt;/goals&gt;
            &lt;configuration&gt;
                &lt;sourceDirectory&gt;src/main/asciidoc/specifications&lt;/sourceDirectory&gt;
                &lt;backend&gt;html5&lt;/backend&gt;
                &lt;sourceHighlighter&gt;highlightjs&lt;/sourceHighlighter&gt;
                &lt;preserveDirectories&gt;true&lt;/preserveDirectories&gt;
                &lt;attributes&gt;
                    &lt;sourceDir&gt;${project.basedir}/src/main/java&lt;/sourceDir&gt;
                    &lt;data-uri/&gt;
                    &lt;toc2/&gt;
                    &lt;toclevels&gt;4&lt;/toclevels&gt;
                &lt;/attributes&gt;
            &lt;/configuration&gt;
        &lt;/execution&gt;
    &lt;/executions&gt;
&lt;/plugin&gt;
[/code]

L'exécution <code>ascidoctor-html5-documentation</code> permet de générer un document HTML5 (le paramètre <code>backend</code>) à partir du dossier source situé dans <code>src/main/asciidoc/specifications</code>, de préserver la hiérarchie des répertoires sources d'AsciiDoctor et d'avoir une table des matières à 4 niveaux, affichée sur le côté (<code>toc2</code>).<sup id="fnref-3637-1"><a href="#fn-3637-1">1</a></sup>.

Voilà ! Maintenant vous pouvez commencer à écrire de l'asciidoctor dans un fichier .adoc:

<pre>
= Projet DEMO - Spécifications Fonctionnelles
================
:author: Yvan VU
:data-uri:
:icons: font

== Introduction

=== Objet du document

Le présent document constitue les spécifications fonctionnelles de l'application DEMO.

=== Documents de référence

D'autres documents sont disponibles pour l'application DEMO :

[cols="1,2", options="header"]
|===
|Titre
|Description
|<<../techniques/index.adoc#,Spécifications_techniques>>
|Spécification techniques du projet DEMO
|===

////
TODO : mettre le lien vers les documents en question
|<<../scenarios.adoc#, Rapport cucumber >>
|Rapport de scénarios du projet DEMO
|<<../index.adoc#,api_rest_docs >>
|Documentation de l'API REST du projet DEMO
////

== Description générale

=== Enjeux

...

=== Usage

TIP: ceci est un pro-tip

==== API REST

l'API REST permettra à chaque client d'utiliser l'application via des appels #http#.

==== FizzBuzz

Lorsque l'on appelle le service FizzBuzz, on lui donne en paramètre un entier. Le service retournera alors :

* l'entier passé en paramètre *sauf*
* si le paramètre est un multiple de *3*, le service retournera *Fizz*
* si le paramètre est un multiple de *5*, le service retournera *Buzz*
* si le paramètre est un multiple de *3 et 5*, le service retournera *FizzBuzz*
</pre>

Comme vous pouvez le voir, la syntaxe d'AsciiDoctor a été faire pour être la plus simple possible afin de ne pas polluer l'information. Les niveaux des titres sont définis par les <code>=</code> et nous avons entre autres créé un tableau, ( les <code>|===</code> et <code>|</code> ), des commentaires et une liste via <code>*</code>.

On peut aussi extraire une partie du code source :
(dans le fichier .adoc)

<pre>
&lsqb;source,java]
----
include::{sourceDir}/com/example/domain/FizzBuzz.java[tags=implementation]
----
</pre>

<code>include</code> permet donc d'inclure le contenu d'un fichier dans la documentation, grâce aux <code>tags</code>, on ira chercher le code java situé entre les balises <code>// tag::implementation[]</code> et <code>// end::implementation[]</code> dans la classe FizzBuzz.

Comme on a défini l’exécution du plugin durant la phase <code>generate-resources</code>, la transformation des documents en html se fera avant la compilation.

Via le build maven, après un simple <code>maven compile</code>, on pourra voir un joli document html se générer dans <code>target/generated-docs/fonctionnelles/index.html</code> :
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/SortieHtmlAsciiDoctor.png"><img class="alignnone size-medium wp-image-3641" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/SortieHtmlAsciiDoctor-300x270.png" alt="SortieHtmlAsciiDoctor" width="300" height="270" /></a>

et pour l'extrait de code :
<img class="alignnone size-medium wp-image-3640" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/FizzBuzz-java.png" alt="FizzBuzz-java" width="600" height="222" />

Voilà, avec un peu de pratique vous maîtriserez AsciiDoctor et écrirez vos propres recettes de cuisine ! Maintenant vous n'avez plus qu'à convaincre votre équipe ou toute personne non technique d'écrire la documentation en asciidoctor et non plus sous word !

<h2>CukeDoctor</h2>

Vous avez donc vos plats à base de Cucumber et des scénarios Gherkin qui n'attendent qu'à être sortis dans une jolie page web montrant que tout est beau :
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/scenariosGherkins.png"><img class="alignnone size-large wp-image-3642" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/scenariosGherkins-1024x441.png" alt="scenariosGherkins" width="910" height="392" /></a>

Là aussi, on va ajouter le plugin maven suivant <sup id="fnref-3637-2"><a href="#fn-3637-2">2</a></sup>:

[code lang=xml]
&lt;plugin&gt;
    &lt;groupId&gt;com.github.cukedoctor&lt;/groupId&gt;
    &lt;artifactId&gt;cukedoctor-maven-plugin&lt;/artifactId&gt;
    &lt;version&gt;0.6.1&lt;/version&gt;
    &lt;configuration&gt;
        &lt;outputFileName&gt;scenarios&lt;/outputFileName&gt;
        &lt;outputDir&gt;generated-docs&lt;/outputDir&gt;
        &lt;toc&gt;left&lt;/toc&gt;
        &lt;numbered&gt;true&lt;/numbered&gt;
    &lt;/configuration&gt;
    &lt;executions&gt;
        &lt;execution&gt;
            &lt;goals&gt;
                &lt;goal&gt;execute&lt;/goal&gt;
            &lt;/goals&gt;
            &lt;phase&gt;install&lt;/phase&gt;
        &lt;/execution&gt;
    &lt;/executions&gt;
&lt;/plugin&gt;
[/code]

La configuration ici présente indique que l'on va générer deux fichiers <code>scenarios.adoc</code> et <code>scenarios.html</code> où tout le report des tests Cucumber sera présent.

Le travail supplémentaire s'arrête là : vous avez déjà écrit tout ce qu'il fallait. Vous avez juste à lancer le build <code>maven install</code> et vous obtiendrez alors la sortie suivante (toujours dans <code>target/generated-docs/scenarios.html</code> :
<img class="alignnone size-full wp-image-3643" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/SortieCukeDoctor.png" alt="SortieCukeDoctor" width="1010" height="355" />

Bon, je vous l'accorde, ceci n'était que pour vous montrer qu'on peut intégrer dans son build l'automatisation de tâches AsciiDoctor, car cela reste un report assez basique au final.
Mais comme il y aussi la génération d'un .adoc à côté du .html, vous pourrez toujours l'importer dans un document asciidoctor, moyennant quelques configurations à faire.
<img src="http://www.storebrandcenter.com/blog/wp-content/uploads/2012/12/Logo-Auchan-MDD_Pouce.jpg" alt="MarquePouce" width="100" height="100" />

Il existe bien sûr d'autres alternatives pour documenter ses tests et rapports Cucumber, du plugin Jenkins au projet github d'un collègue.

Retrouvons-nous après une courte page de publicité : Vous souhaitez en faire plus avec vos concombres ? Vous en avez marre de juste les découper en rondelle et les présenter en apéro à ses amis? Découvrez <a href="https://github.com/Arnauld/tzatziki" target="_blank">Tzatziki</a>, une recette onctueuse préparé par <a href="https://twitter.com/aloyer" target="_blank">Arnauld Loyer</a> lui-même !

<h2>Spring REST Docs</h2>

Attention, c'est à cet endroit qu'on utilise notre ingrédient principal, l'ingrédient que l'on aime tous : Spring !

Les rouleaux de printemps, c'est facile à faire. Idem ici aussi, on commence par rajouter la levure chimique à notre maven puis 2 nouvelles tâches à exécuter dans la partie build (qui commence à grossir maintenant...) :
l'une pour définir et filtrer les classes de tests pour Spring REST Docs, l'autre, qui est une nouvelle exécution du plugin AsciiDoctor, pour transformer le document de l'API REST en html.

[code lang=xml]
&lt;!-- dans dependencies --&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.restdocs&lt;/groupId&gt;
    &lt;artifactId&gt;spring-restdocs-mockmvc&lt;/artifactId&gt;
    &lt;version&gt;1.0.0.RELEASE&lt;/version&gt;
    &lt;scope&gt;test&lt;/scope&gt;
&lt;/dependency&gt;
&lt;!-- à ajouter dans executions de asciidoctor-maven-plugin, après l&#039;execution du ascidoctor-html5-documentation --&gt;
&lt;execution&gt;
    &lt;id&gt;rest-api&lt;/id&gt;
    &lt;phase&gt;package&lt;/phase&gt;
    &lt;goals&gt;
        &lt;goal&gt;process-asciidoc&lt;/goal&gt;
    &lt;/goals&gt;
    &lt;configuration&gt;
        &lt;sourceDirectory&gt;src/main/asciidoc/api&lt;/sourceDirectory&gt;
        &lt;backend&gt;html5&lt;/backend&gt;
        &lt;sourceHighlighter&gt;highlightjs&lt;/sourceHighlighter&gt;
        &lt;attributes&gt;
            &lt;sourceDir&gt;${project.basedir}/src/main/java&lt;/sourceDir&gt;
            &lt;generatedDir&gt;${project.build.directory}/generated-snippets&lt;/generatedDir&gt;
            &lt;data-uri/&gt;
            &lt;toc2/&gt;
            &lt;toclevels&gt;4&lt;/toclevels&gt;
        &lt;/attributes&gt;
    &lt;/configuration&gt;
&lt;/execution&gt;
&lt;!-- à ajouter build &gt; plugins --&gt;
&lt;plugin&gt;
    &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
    &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;
    &lt;version&gt;2.18.1&lt;/version&gt;
    &lt;executions&gt;
        &lt;!--tests for api rest documentation--&gt;
        &lt;execution&gt;
            &lt;id&gt;api-rest-documentation-tests&lt;/id&gt;
            &lt;goals&gt;
                &lt;goal&gt;test&lt;/goal&gt;
            &lt;/goals&gt;
            &lt;configuration&gt;
                &lt;includes&gt;
                    &lt;include&gt;**/*RESTDocumentation.java&lt;/include&gt;
                &lt;/includes&gt;
                &lt;systemPropertyVariables&gt;
                    &lt;org.springframework.restdocs.outputDir&gt;
                        ${project.build.directory}/generated-snippets
                    &lt;/org.springframework.restdocs.outputDir&gt;
                &lt;/systemPropertyVariables&gt;
            &lt;/configuration&gt;
        &lt;/execution&gt;
    &lt;/executions&gt;
&lt;/plugin&gt;
[/code]

L’exécution supplémentaire de AsciiDoctor pourrait être fusionnée avec celle de la 1ère étape : si vous souhaitez le faire, vous devrez déplacer la génération asciidoctor après la phase de tests. En effet, la phase de tests génère les rapports Cucumber et Spring Rest Docs en asciidoctor, la transformation asciidoctor pourra alors l'inclure et s'exécuter dans une phase tardive comme la package.

Nos tests de webservices REST réalisés avec mockmvc sont donc écrits comme ceci :

[code lang=java]
@RunWith(SpringJUnit4ClassRunner.class)
@SpringApplicationConfiguration(classes = {DemoApplication.class})
@WebAppConfiguration
public class FizzBuzzRestTests {

    @Rule
    public final RestDocumentation restDocumentation = new RestDocumentation(&quot;target/generated-snippets&quot;);
    @Autowired
    private WebApplicationContext context;
    @Autowired
    private ObjectMapper objectMapper;
    private MockMvc mockMvc;

    @Before
    public void setUp() {
        objectMapper.findAndRegisterModules();
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context)
                .apply(documentationConfiguration(this.restDocumentation))
                .build();
    }

    @Test
    public void checkCodeValiditySimple() throws Exception {
        this.mockMvc.perform(
                get(&quot;/fizzbuzz/{input}&quot;, 30)
                        .accept(MediaType.APPLICATION_JSON)
                        .contentType(MediaType.APPLICATION_JSON))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(content().string(&quot;{&quot;result&quot;:&quot;FizzBuzz&quot;}&quot;))
                .andDo(document(&quot;fizz-buzz&quot;,
                                pathParameters(
                                        parameterWithName(&quot;input&quot;).description(&quot;Un paramètre entier.&quot;)),
                                responseFields(
                                        fieldWithPath(&quot;result&quot;).description(&quot;Le résultat de l&#039;appel : L&#039;entier passé en paramètre, Fizz, Buzz ou FizzBuzz&quot;))
                        )
                );
    }

}
[/code]

Ici, dans le test reposant sur mockmvc du controller correspondant à <code>/fizzbuzz/{entier}</code>, on a ajouté dans le <code>@Before</code> la configuration pour utiliser REST Docs. Puis dans le test, on documente dans le <code>andDo(document(...))</code> les paramètres en entrée et sortie. Cela veut dire que si vous avez déjà des tests sur vos controllers Spring MVC, vous n'avez qu'à rajouter cette partie.
Pour l'exemple, on retourne un object JSON contenant le résultat du fizzbuzz.

Lorsque ce test sera exécuté via maven, plusieurs documents seront générés dans <code>target/generated-snippets/fizz-buzz</code>:

<ul>
<li>curl-request.adoc</li>
<li>http-request.adoc</li>
<li>http-response.adoc</li>
<li>path-parameters.adoc</li>
<li>response-fields.adoc</li>
</ul>

Vous pouvez alors utiliser ces documents en les incluant à votre documentation pour l'API REST :

[code lang=text]
=== GET /fizzbuzz/{entier}

Requête GET pour récupérer le résultat d&#039;un fizzbuzz particulier d&#039;un {entier} passé en paramètre de l&#039;URL.

Requête curl :
include::{generatedDir}/fizz-buzz/curl-request.adoc[]

Requête http :
include::{generatedDir}/fizz-buzz/http-request.adoc[]

Paramètres :
include::{generatedDir}/fizz-buzz/path-parameters.adoc[]

&#039;&#039;&#039;

Réponse http :
include::{generatedDir}/fizz-buzz/http-response.adoc[]

Champs de la réponse :
include::{generatedDir}/fizz-buzz/response-fields.adoc[]

=== POST ...

...
[/code]

Le plugin maven va donc transformer ces tests en une documentation asciidoctor (qui sera à son tour transformée en html via l'étape du plugin maven asciidoctor) consultable dans <code>target/generated-docs/index.html</code> :
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/SortieRestDocs.png" target="_blank"><img class="alignnone size-medium wp-image-3644" src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/SortieRestDocs-278x300.png" alt="SortieRestDocs" width="278" height="300" /></a>

Spring Rest Docs est capable de documenter les paramètres dans le path aussi bien que dans la request, de gérer l'hypermedia, les headers et les payload en JSON ou XML.
Votre exemple d'appel pouvant très bien comporter des champs optionnels, vous pouvez spécifier le type d'un champ afin que Spring Rest Docs ne le mette pas à Null.
Du coup, vous pouvez faire plusieurs exemples d'appels et puisque vous avez juste à extraire la partie document() dans une méthode, vous ne documentez qu'à un seul endroit du code !

Avec ceci, vous avez une documentation de votre API REST qui est claire, concise, et qui, en se reposant sur vos tests, devient vivante car tout changement d'api vous obligera à revoir la documentation de vos exemples.
<img src="https://v.cdn.vine.co/r/avatars/4F55CDC0651131131937658966016_2dcfc179843.5.1.jpg" alt="ThumbUpKid" width="100" height="100" />

<h1>Dessert</h1>

Grâce à ces 3 solutions, vous avez maintenant la possibilité d'avoir une documentation vivante de votre code qui servira :

<ul>
<li>au métier pour la partie spécifications (et évitera d'aller rechercher dans vos mails le fameux "[FINAL]LES_VERITABLES_ET_UNIQUE_COMPLETE_specifications_v13.87(copier)(6).docx")</li>
<li>aux autres développeurs qui interviendront tôt ou tard sur le code et pourront s'appuyer sur les différents documents</li>
<li>aux personnes qui utiliseront votre API et auront des exemples d'appels</li>
</ul>

Vous venez donc de sublimer votre délicieux projet Spring. Vous ne l'utilisez pas à votre travail ? Pas de problème, la 1ère partie est la plus importante et la plus générique.

On peut aussi noter que l'ensemble de ces solutions n'impactent aucunement l'environnement de travail puisqu'elles sont directement intégrées via des plugins maven.

Reste ensuite que votre documentation doit être déposée sur un serveur web accessible par les personnes visées. Personnellement (et en tant que réinventeur d'omelette), j'ai intégré un script à la fin de mon build Jenkins pour copier les pages html sur un répertoire de serveur apache disponible en intranet. Sinon, vous pouvez toujours l'intégrer à un <a href="http://asciidoctor.org/docs/asciidoctor-maven-plugin/#maven-site-integration" target="_blank">maven site</a>.

Vous pouvez retrouver le code utilisé pour cet article sur ce <a href="https://github.com/atrolla/demo_asciidoctor_article_arolla" target="_blank">GitHub.</a>

<div class="footnotes">
<hr />
<ol>

<li id="fn-3637-1">
Vous trouverez plus d'exemples de configuration à l'adresse github du projet : <a href="https://github.com/asciidoctor/asciidoctor-maven-plugin" target="_blank">https://github.com/asciidoctor/asciidoctor-maven-plugin</a>.&#160;<a href="#fnref-3637-1">&#8617;</a>
</li>

<li id="fn-3637-2">
Lien github du plugin Maven CukeDoctor: <a href="https://github.com/rmpestano/cukedoctor/tree/master/cukedoctor-maven-plugin" target="_blank">https://github.com/rmpestano/cukedoctor/tree/master/cukedoctor-maven-plugin</a>.&#160;<a href="#fnref-3637-2">&#8617;</a>
</li>

</ol>
</div>
