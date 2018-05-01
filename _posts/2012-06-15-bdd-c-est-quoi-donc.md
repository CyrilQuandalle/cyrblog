---
layout: post
title: Le BDD, qu'est ce que c'est?
date: 2012-06-15 10:47
author: arnauld loyer
comments: true
categories: [Agilité, bdd, Bonnes pratiques de dév, jbehave, Programmation]
---
<p style="text-align: justify;">A l'occasion de la release <a href="http://github.com/Arnauld/jbehave-eclipse-plugin"><code>1.0.7</code> du plugin Eclipse</a> pour <a href="http://jbehave.org/">JBehave</a>, je vais en profiter pour présenter cet outil, à quoi il peut servir et comment le mettre en oeuvre.</p>

<p style="text-align: justify;"><a href="http://jbehave.org/">JBehave</a> est un framework BDD pour Java et Groovy.</p>

<p style="text-align: justify;"><strong> BDD quézako!? </strong></p>

<p style="text-align: justify;">Tout d'abord un peu d'histoire: le BDD encore un acronyme du type <strong>xDD</strong>? Et bien oui, encore un! Le BDD (Behavior Driven Development) est présenté comme une évolution du TDD (Test Driven Development).
Proposé par Dan North (qui fut aussi l'un des initiateurs du projet JBehave), le BDD consiste à étendre le TDD en écrivant non plus du code compréhensible uniquement par des développeurs, mais sous forme de scénario compréhensible par toutes les personnes impliquées dans le projet.</p>

<p style="text-align: justify;">Autrement dit, il s'agit d'écrire des tests qui décrivent le comportement attendu du système et que tout le monde peux comprendre.</p>

<p style="text-align: justify;">Et le rapport avec le TDD? Eh bien généralement, ces scénarios sont écrits et définis avant que l'implémentation ne commence. Ils servent à la fois à définir le besoin mais également vont guider le développement en le focalisant sur la fonctionnalité décrite. Dans l'absolu, on continue à faire du TDD mais on ajoute en plus l'expression du besoin en langage naturel. Alors que le TDD garantit d'une certaine façon la qualité technique d'une implémentation, il ne garantit pas la qualité fonctionnelle. Plusieurs éléments peuvent ainsi être techniquement valides mais une fois mis ensemble, ils ne répondent pas du tout au besoin réellement exprimé par le client. De manière un peu caricaturale, le BDD va guider le développement d'une fonctionnalité, tandis que le TDD guidera son implémentation.</p>

<p style="text-align: justify;">Les avantages d'une telle pratique sont multiples (les différents "points" réfèrent à la figure ci-après):</p>

<p style="text-align: justify;"><strong>Point (1): Un dialogue restauré</strong></p>

<ul style="text-align: justify;">
    <li><strong>L'écriture des scénarios se fait de manière collective</strong>: développeurs, clients, équipe support, ...: tout le monde peut participer à <strong>l'expression du besoin</strong>, puisque celui-ci se fait <strong>en langage naturel</strong>. Toutes les questions soulevées, par le client ou par le développeur, peuvent faire l'objet de scénarios dédiés. <strong>Les scénarios décrivent alors le fonctionnement réel de l'application</strong>, et le traitement des cas aux limites.</li>
</ul>

<p style="text-align: justify;"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/bdd-dialogue.png" alt="BDD un dialogue" /></p>

<ul style="text-align: justify;">
    <li>En illustrant chaque cas d'utilisation par <strong>des exemples concrets</strong>, <strong>les règles métiers sont moins abstraites et mieux comprises</strong>.</li>
    <li>En écrivant des tests que tout le monde peut comprendre, les clients s'assurent que les développeurs ont bien compris les besoins métiers, les développeurs sortent de leur “autisme” (<a href="http://herdingcode.com/?p=176">Herding Code #42 ~ Scott Bellware on BDD</a>) et se rapprochent du métier. Les deux parties avancent ensemble dans la même direction. Cela constitue un véritable <strong>outil de communication</strong> rapprochant développeurs et personnes du métiers, et restaure la confiance du client en rendant le comportement explicite et visible.</li>
    <li>A chaque fois qu'une nouvelle spécification est écrite, son utilisation est illustrée par plusieurs exemples et cas de tests: cela force à réfléchir sur la fonctionnalité et son utilisation. Il est possible de communiquer avec tout le monde et pas seulement les développeurs, il est ainsi <strong>plus facile d'inciter les gens à s'impliquer</strong> sur ce qui est fait: d'avoir des fonctionnalités mieux décrites et plus challengées. Cela permet aussi de rassurer le développeur sur l’intérêt de ce développement.</li>
    <li><strong>En utilisant le contexte métier pour décrire les fonctionnalités</strong> souhaitées, il y a moins de digressions et de considérations techniques dans l'expression des besoins: a-t-on besoin de savoir qu'un message d'erreur sur une interface Web est un élément HTML avec la classe 'error'? Non, on souhaite seulement s'assurer qu'en cas de saisie erronée, un message d'erreur est présent et ce, dans le contexte d'une page web. <strong>Le contexte métier sert alors de filtre dans l'expression des besoins</strong>, libérant le développeur et les personnes du métier de détails techniques et d'implémentation.</li>
    <li>Le scénario sert à focaliser les discussions et la réalisation sur les attentes du client. En guidant  l'écriture du code, celui-ci devient plus fonctionnel et plus imprégné du métier.</li>
    <li>La <strong>structuration du scénario permet de formaliser l'expression des besoins avec un langage commun et facilement interprétable</strong>. Ces mots clés (<code>Given</code>, <code>When</code>, <code>Then</code>, <code>As a</code>, <code>In order to</code> ...) permettent de définir une grammaire commune qui sert à la fois à structurer le scénario en langage naturel, et sert aux outils comme <a href="http://jbehave.org">JBehave</a> à faire la correspondance avec le code.</li>
</ul>

<p style="text-align: justify;"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/bdd-overview.png" alt="BDD apperçu général" /></p>

<p style="text-align: justify;"><strong>Point (2): Un développement guidé et documenté</strong></p>

<ul style="text-align: justify;">
    <li><strong>Le besoin fonctionnel guide le développement de l'application</strong>: on développe ce dont on a besoin; et on limite les écueils de cathédrale technologique.</li>
    <li><strong>Les comportements deviennent documentés</strong>: même si la <code>javadoc</code> ou les commentaires permettent de documenter le code, il existe rarement de <strong>documentation sur le comportement réel de l'application</strong>; on dispose ainsi d'exemples concrets.</li>
    <li>Les tests unitaires sont généralement très riches sur le comportement d'une application, mais ils restent très hermétiques à des non-développeurs (parfois même aux autres développeurs et aux nouveaux arrivants), en les rendant plus accessibles et plus lisibles, <strong>ils constituent une réelle source d'information sur le comportement de l'application</strong>, et sont toujours à jour.</li>
</ul>

<p style="text-align: justify;"><strong>Point (3): Une documentation à jour et toujours disponible</strong></p>

<ul style="text-align: justify;">
    <li>En étant directement exécutables, les histoires intègrent directement la base de code: <strong>elles sont archivées avec le code qui les exécute</strong>. Les histoires et leurs modifications évoluent donc naturellement en même temps que la base de code. Avec un <em>astucieux</em> jeu de tag et de branche, on peux donc facilement documenter les évolutions fonctionnelles du code.</li>
</ul>

<p style="text-align: justify;"><strong>Point (4): Des tests en continu sur les fonctionnalités
</strong></p>

<ul style="text-align: justify;">
    <li>Ce point rejoint très fortement les points précédents: <strong>l'environnement d'intégration continue teste en permanence les comportements fonctionnels réels</strong> au fil des modifications du code, et non plus seulement l'implémentation technique.</li>
</ul>

<p style="text-align: justify;">Si l'on devait résumer en une phrase:</p>

<p style="text-align: justify;"><strong><em>Il s'agit d'une méthodologie de travail, permettant d'écrire des tests compréhensibles à la fois par le client et par le développeur et s'intégrant directement dans la base de code.</em></strong></p>

<p style="text-align: justify;">On notera que l'on parle bien <strong>de tests au sens général</strong>! Cette méthodologie peut en effet aussi bien s'appliquer à des tests unitaires, des tests fonctionnels, des tests d'intégrations, des tests de bout en bout, etc. Quelle que soit la taxonomie utilisée, <strong>tout type de test pourrait s'exprimer à l'aide de scénario</strong>.</p>

<p style="text-align: justify;">Il s'agit d'ailleurs d'un travers que l'on rencontre souvent: associer systématiquement cette méthodologie avec l'écriture de tests d'intégration. Si l'on évoque <a href="http://jbehave.org">JBehave</a>, pour beaucoup l'association est rapidement faite avec <a href="http://code.google.com/p/selenium/wiki/GettingStarted">Selenium</a> et l'écriture de tests d'intégration d'interface Web.</p>

<p style="text-align: justify;"><img style="float: left; margin: 5px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/C-est-dit.png" alt="Démystification" width="150px" /> <em>Eh bien non! Tout type de tests peut s'écrire avec les principes évoqués. En fait, la plupart des outils de BDD (JBehave, Cucumber, Easyb, ...) ne font <strong>"que"</strong> la traduction d'un scénario en langage naturel en appels de méthodes</em>. Ce que les méthodes pilotent réellement tient uniquement de leur contenu et non de l'outil utilisé pour les appeler. Les outils définissent une grammaire permettant de faire correspondre le scénario avec le code qui sera appelé. Chaque étape est généralement reliée à une fonction ou une méthode particulière qui pilote un changement d'état ou une action.</p>

<p style="text-align: justify;"><img src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/bdd-fmk-traduction.png" alt="BDD la traduction d'une story en appel de code" /></p>

<p style="text-align: justify;"><strong>De plus, il est tout à fait possible (et même fortement conseillé) d'utiliser ce type de tests même si ceux-ci ne guident pas le développement et sont écrits <em>a posteriori</em>: la source documentaire qu'ils procurent est presque aussi riche que le code qu'ils manipulent.</strong></p>

<h2 style="text-align: justify;">Formalisme et structure des histoires</h2>

<p style="text-align: justify;"><img style="float: left; width: 150px; margin-right: 5px; margin-bottom: 5px;" src="http://www.arolla.fr/blog/wp-content/uploads/2012/06/bdd-structureStory.png" alt="Structure d'une histoire" /></p>

<p style="text-align: justify;">Avant d'illustrer cela par quelques exemples, voyons le formalisme standard utilisé pour écrire ces scénarios.</p>

<p style="text-align: justify;">Tout d'abord <strong>le préambule</strong> ou <strong>Narrative</strong>.
Il s'agit de l'entête d'une histoire qui est commun à un ensemble de scénarios. Il permet de placer le contexte général et de décrire très brièvement les fonctionnalités qui vont être présentées. En général, ce préambule n'est qu'illustratif et ne pilote pas de code.</p>

<table>
<tbody>
<tr>
<td><strong>As a</strong> [rôle],</td>
<td><strong>En tant que</strong> [rôle ou personne],</td>
</tr>
<tr>
<td><strong>I want</strong> [behavior]</td>
<td><strong>Je veux</strong> [fonctionnalité]</td>
</tr>
<tr>
<td><strong>In order to</strong> [outcome]</td>
<td><strong>Afin de</strong> [but, bénéfice ou valeur de la fonctionnalité]</td>
</tr>
</tbody>
</table>

<p style="text-align: justify;">Une histoire étant constituée d'un ou plusieurs scénarios, vient ensuite la <strong>description sommaire du scénario</strong> qui va être déroulée, son titre:</p>

<table>
<tbody>
<tr>
<td><strong>Scenario:</strong> [scenario]</td>
<td><strong>Scénario:</strong> [description]</td>
</tr>
</tbody>
</table>

<p style="text-align: justify;">Enfin <strong>le contenu du scénario</strong>. Le scénario est une succession d'étapes (<code>Step</code>) permettant:</p>

<ul style="text-align: justify;">
    <li>soit de définir et de construire le contexte dans lequel le scénario va se dérouler <code>Given</code>,</li>
    <li>soit de provoquer des événements ou des actions sollicitant le système <code>When</code>,</li>
    <li>soit de vérifier que le comportement attendu a bien eu lieu <code>Then</code>; c'est généralement à ces étapes que l'on retrouvera les assertions.</li>
</ul>

<table>
<tbody>
<tr>
<td><strong>Given</strong> [an initial context]</td>
<td><strong>Etant donné</strong> [un contexte initial (les acquis)]</td>
</tr>
<tr>
<td><strong>When</strong> [action]</td>
<td><strong>Quand</strong> [un événement survient]</td>
</tr>
<tr>
<td><strong>Then</strong> [expected result]</td>
<td><strong>Alors</strong> [on s'assure de l'obtention de certains résultats]</td>
</tr>
</tbody>
</table>

<p style="text-align: justify;">Cette grammaire (à base de mots clés en début de phrase) permet de définir le langage de nos scénarios. Il est de plus en plus souvent appelé <strong>langage Gherkin</strong>, nom donné et popularisé par le framework <a>Cucumber</a> (voir <a>Cucumber-jvm</a> pour un <em>"portage disutable"</em> en Java).</p>

<h2 style="text-align: justify;">Flash info !</h2>

<p style="text-align: justify;">Excusez-nous pour cette interruption de programme, voici quelques actualités:</p>

<blockquote class="twitter-tweet">. @<a href="https://twitter.com/mgrimes">mgrimes</a> BDD does not mean UI testing!Far from it.Cucumber users have got to get this straight!

— Uncle Bob Martin (@unclebobmartin) <a href="https://twitter.com/unclebobmartin/status/207183252950224896">May 28, 2012</a></blockquote>

<blockquote class="twitter-tweet">. @angelaharms BDD is TDD with good naming conventions. The ideas are good. The name is confusing. It's really just TDD.

— Uncle Bob Martin (@unclebobmartin) <a href="https://twitter.com/unclebobmartin/status/207281653582802944">May 29, 2012</a></blockquote>

<blockquote class="twitter-tweet">@glanotte No. BDD _not_ equal acceptance tests. BDD just better worded tests.

— Uncle Bob Martin (@unclebobmartin) <a href="https://twitter.com/unclebobmartin/status/207323686880022528">May 29, 2012</a></blockquote>

<p style="text-align: justify;">Et la réponse de Dan North: <a href="http://dannorth.net/2012/05/31/bdd-is-like-tdd-if/">BDD is like TDD if...</a>.</p>

<h2 style="text-align: justify;">That's all Folks (pour aujourd'hui!)</h2>

<p style="text-align: justify;">Nous venons de voir ce qu'est le BDD et son formalisme, avant de poursuivre faisons une petite pause pour digérer tout cela. Dans notre prochain article, nous verrons comment mettre tout cela en œuvre en codant un exemple très simple avec JBehave.</p>
