---
layout: post
title: "Paris JUG :  Soirée Intégration avec Spring Intégration et Apache Camel"
date: 2013-10-23 10:15
author: yakhya dabo
comments: true
categories: [Apache Camel, Bonnes pratiques de dév, EIP, Evénements, Spring, Spring Integration]
---
<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/parisjug.png"><img class=" wp-image-1826 alignleft" alt="parisjug" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/parisjug-262x300.png" width="157" height="180" /></a>Un Système d'Information vit et grossit. Au cour de son expansion, il assiste à la naissance de nouvelles technos, les voit vieillir et devenir obsolètes. Parallèlement le nombre d'applications à intégrer ne cesse de croître, avec chacune ses technos, ses formats de données et ses protocoles de communication. Le domaine métier aussi évolue au même rythme que les renouvellements de personnel. Il est très peu pratique de concevoir une application pour qu'elle reste isolée. Quand elle n'est pas appelée à fournir des services ou à échanger des données avec d'autres applications, elle peut avoir besoin de s'appuyer sur de l'existant pour implémenter une fonctionnalité.</p>

<p style="text-align: justify;">L'un des enjeux de ce contexte est de simplifier les échanges entre applications, tout en tenant compte des différentes contraintes inhérentes à un SI hétérogène (domaine métier, fiabilité et lenteur réseau, protocole, formats de données…).</p>

<p style="text-align: justify;">Un des travaux les plus aboutis dans le domaine de l'intégration entre applications est celui de <strong>Gregor Hohpe</strong> et <strong>Bobby Woolf</strong>. Leur livre “Enterprise Integration Patterns” (EIP) , <a href="http://martinfowler.com/books/eip.html">préfacé par Martin Fowler</a>, constitue LA référence sur le sujet. Il y est présenté un catalogue de 65 patterns pour répondre aux problèmes couramment rencontrés.</p>

<p style="text-align: justify;">Dans l'écosystème Java trois frameworks (<strong>Apache Camel</strong>, <strong>Spring Integration</strong> et <strong>Mule</strong>) fournissent chacun son implémentation des EIP à sa façon. Sur internet les blogs et les forums abondent déjà de sujets sur les points forts et les points faibles de chacun ainsi que des critères de choix.</p>

<p style="text-align: justify;">Voilà une brève présentation du thème de la soirée Paris JUG du 08 Octobre 2013 présentée par <b>Guillaume Giamarchi</b> et <b>Grégory Boissinot</b> de la société <b>Zenika</b> dans les locaux de l'<strong>ISEP</strong> . Il y avait deux présentations, la première consacrée à l'EIP (Entreprise Integration Pattern) et la deuxième partie sur Spring Integration et Apache Camel, deux implémentations des EIP.</p>

Au fil des ans l'ingénierie logicielle a vu apparaitre différents styles d'intégration :

<p style="text-align: justify;"><b>File Transfert :</b> Les applications communiquent par le biais d'un fichier où elles viennent écrire ou lire par intervalle de temps régulier. Un style qui pose le problème des accès concurrents.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/FileTransferIntegration2.gif"><img class="alignnone size-medium wp-image-1825" alt="FileTransferIntegration" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/FileTransferIntegration2-300x105.gif" width="300" height="105" /></a></p>

<p style="text-align: justify;"><b>Shared Data Base</b> : Ce système fonctionne comme le FT, avec des données stockées en base. Il soulève naturellement des problématiques de Transaction.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/SharedDatabaseIntegration.gif"><img class="alignnone size-full wp-image-1820" alt="SharedDatabaseIntegration" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/SharedDatabaseIntegration.gif" width="286" height="155" /></a></p>

<p style="text-align: justify;"><b>RPC (Remote Control Process) :</b> Ici l'information est encapsulée dans une invocation distante (RMI et Corba). A demande à B d'effectuer une action et de lui retourner le résultat. On rencontre ici un problème de dépendance entre les deux applications, A doit connaître les interfaces de B.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/EncapsulatedSynchronousIntegration1.gif"><img class="alignnone size-medium wp-image-1822" alt="EncapsulatedSynchronousIntegration" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/EncapsulatedSynchronousIntegration1-300x122.gif" width="300" height="122" /></a></p>

<p style="text-align: justify;"><b>Messaging System :</b> L'échange se fait par le biais d'un bus de messages où les données transitent. La communication se fait de manière asynchrone. JMS et AMQP sont des exemples de Messaging.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/Messaging.gif"><img class="alignnone size-medium wp-image-1823" alt="Messaging" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/Messaging-300x100.gif" width="300" height="100" /></a></p>

<p style="text-align: justify;">Parmi ces quatre styles d'intégration le Messaging est celui qui répond le mieux, grâce à sa flexibilité, aux problèmes rencontrés. Il permet un couplage faible entre les applications. Dans ce système on se focalise plus sur la structure des données échangées que sur la manière dont elles sont produites.</p>

Le système de Messaging est structuré autour de six concepts qui forment les briques de bases pour la conception d'un système.

<p style="text-align: justify;"><b>Message :</b> Les données échangées entre deux applications sont encodées dans un message. Il est composé d'un header et d'un body.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageIcon.gif"><img class="alignnone size-full wp-image-1830" alt="MessageIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageIcon.gif" width="50" height="54" /></a></p>

<p style="text-align: justify;"><b>Channel :</b> Il établit la liaison entre deux applications. C'est le chemin virtuel par lequel sont transmis (et reçus) les Messages.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/ChannelIcon.gif"><img class="alignnone size-full wp-image-1831" alt="ChannelIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/ChannelIcon.gif" width="86" height="54" /></a></p>

<p style="text-align: justify;"><b>Pipe &amp; Filter :</b> C'est un enchainement de traitements, dans lequel la sortie d'un traitement est utilisé comme entrée du traitement suivant.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/PipesAndFiltersIcon.gif"><img class="alignnone size-full wp-image-1832" alt="PipesAndFiltersIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/PipesAndFiltersIcon.gif" width="86" height="54" /></a></p>

<p style="text-align: justify;"><b>Routing :</b> Le routing consiste à consommer un message (sans l'altérer) et à choisir une sortie en fonction de conditions déterminées dynamiquement.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/ContentBasedRouterIcon.gif"><img class="alignnone size-full wp-image-1834" alt="ContentBasedRouterIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/ContentBasedRouterIcon.gif" width="86" height="54" /></a></p>

<p style="text-align: justify;"><b> Transformer :</b> Le transformer, comme son l'indique, transforme le message consommé à l'entrée pour à la sortie.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageTranslatorIcon.gif"><img class="alignnone size-full wp-image-1833" alt="MessageTranslatorIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageTranslatorIcon.gif" width="86" height="54" /></a></p>

<p style="text-align: justify;"><b> End point : </b>C'est le point d'entrée/sortie d'une application, son interface avec l'extérieur. C'est lui qui encode le Message (en lui ajoutant un header) avant de le mettre sur un channel. A l'autre bout, le message est également reçu et décodé (extraction du content) par un End Point.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageEndpointIcon.gif"><img class="alignnone size-full wp-image-1835" alt="MessageEndpointIcon" src="http://www.arolla.fr/blog/wp-content/uploads/2013/10/MessageEndpointIcon.gif" width="82" height="49" /></a></p>

<h2 style="text-align: center;">Spring Integration</h2>

<p style="text-align: justify;">C'est l'implémentation de Spring pour les EIP. Ses principaux composants sont le <em>Message</em>, le <em>MessageChannel</em>, et le <em>MessageEndpoint</em>. Il bénéficie en tant que partie intégrante de Spring portfolio des fonctionnalités de base de Spring core, lifecycle management, Task Execution, Aspect-Oriented Progaramming, Transaction Management, Dependancy Injection, Method Invocation,... Il est fourni avec des adaptateurs sur étagères pour les utilisations classiques (AMQP, IMAP, JMS, Mongo DB, FTP/SFTP, …). Il est fourni avec une API programmatique mais trouve sa force dans son modèle déclaratif qui permet d'avoir un couplage faible entre le métier et l'infrastructure :</p>

<ul>
    <li>le métier dans le code java</li>
</ul>

<ul>
    <li>la logique d'intégration dans un fichier XML</li>
</ul>

<strong>Exemple avec AMQP :</strong>

[xml]
&amp;lt;!-- End Point --&amp;gt;
 &amp;lt;int-amqp:inbound-channel-adapter
 queue-names=&amp;quot;testQueue&amp;quot;
 channel=&amp;quot;channel1&amp;quot;
 ...
 /&amp;gt;

&amp;lt;!-- Channel --&amp;gt;
 &amp;lt;int:channel id=&amp;quot;channel1&amp;quot;&amp;gt;
 &amp;lt;int:interceptors&amp;gt;
 &amp;lt;int:wire-tap channel=&amp;quot;loggingChannel&amp;quot; /&amp;gt;
 &amp;lt;/int:interceptors&amp;gt;
 &amp;lt;/int:channel&amp;gt;

&amp;lt;!-- Transformer --&amp;gt;
 &amp;lt;int:transformer input-channel=&amp;quot;channel1&amp;quot; output-channel=&amp;quot;channel2&amp;quot;&amp;gt;
 &amp;lt;bean class=&amp;quot;com.spring.jug.CustomTransformer&amp;quot;&amp;gt;
 &amp;lt;/int:transformer&amp;gt;

&amp;lt;!-- Channel --&amp;gt;
 &amp;lt;int:channel id=&amp;quot;channel2&amp;quot;&amp;gt;
 &amp;lt;int:interceptors&amp;gt;
 &amp;lt;int:wire-tap channel=&amp;quot;loggingChannel&amp;quot; /&amp;gt;
 &amp;lt;/int:interceptors&amp;gt;
 &amp;lt;/int:channel&amp;gt;

&amp;lt;!-- MessageHandler --&amp;gt;
 &amp;lt;int:service-activator input-channel=&amp;quot;channel2&amp;quot;
 output-channel=&amp;quot;channel3&amp;quot;
 ref=&amp;quot;customMessageHandler&amp;quot;/&amp;gt;
[/xml]

<h2 style="text-align: center;">Apache Camel</h2>

<p style="text-align: justify;">Ici le composant principal est le Route. Apache Camel fournit plus de components sur étagère que Spring Integration, avec près de 150 connecteurs. En plus il peut s'intégrer facilement avec Spring Core. Tout comme Spring Integration on peut décrire avec Apache Camel le système de Messaging dans un fichier XML. Mais son originalité se trouve dans l'utilisation d'un DSL Java, (qui existe aussi en Scala ou Groovy) avec lequel il exprime le routing dans le code. Les composants sont représentés par des méthodes, <em><strong>from()</strong></em> et <em><strong>to()</strong></em> pour le EndPoint, <strong><em>choice()</em></strong> et <em><strong>split()</strong></em> pour le Routing, ...</p>

<strong>Exemple XML</strong>

[xml]
&amp;lt;camelContext id=&amp;quot;camelTargetContext&amp;quot; … &amp;gt;
&amp;lt;camelContext id=&amp;quot;camelTargetContext&amp;quot; … &amp;gt;
    &amp;lt;route&amp;gt;
        &amp;lt;from uri=&amp;quot;direct:EndpointA&amp;quot; /&amp;gt;
        &amp;lt;to uri=&amp;quot;mock:result&amp;quot; /&amp;gt;
    &amp;lt;/route&amp;gt;
    &amp;lt;route&amp;gt;
        &amp;lt;from uri=&amp;quot;direct:EndpointC&amp;quot;/&amp;gt;
        &amp;lt;process ref=&amp;quot;myProcessor&amp;quot;/&amp;gt;
      &amp;lt;/route&amp;gt;
&amp;lt;/camelContext&amp;gt;
[/xml]

<strong>Exemple Java</strong>

[java]
from(&amp;quot;direct:EndpointA&amp;quot;).to(&amp;quot;mock:result&amp;quot;);

from(&amp;quot;direct:EndpointC&amp;quot;).recipientList().method(&amp;quot;drinkRouter&amp;quot;);

from(&amp;quot;seda:coldDrinks?concurrentConsumers=2&amp;quot;)
.to(&amp;quot;bean:barista?method=prepareColdDrink&amp;quot;)
.to(&amp;quot;direct:deliveries&amp;quot;);
[/java]

<h2>Conclusion</h2>

<p style="text-align: justify;">Apache Camel et Spring Intégration sont deux solutions des EIP qui ont des approches différentes. Pour un habitué de Spring la courbe d'apprentissage est nulle pour démarrer avec Spring Integration. On y retrouvera tous les concepts de Spring Core. Mais le DSL de Apache Camel a l'avantage de présenter la logique d'intégration de manière plus lisible, plus facile à maintenir. Si vous voulez en connaître plus sur les éléments de comparaison vous pouvez jeter un coup d’œil sur les <a href="http://www.slideshare.net/KaiWaehner/camelone-2012-spoilt-for-choice-which-integration-framework-to-use">slides</a> de <strong>Kai Wähner</strong> sur les points faibles/forts de chacun des frameworks.</p>

<strong>Pour plus de lecture</strong>

<ul>
    <li><a href="http://www.eaipatterns.com" target="_blank">http://www.eaipatterns.com</a></li>
    <li><a href="http://camel.apache.org/" target="_blank">http://camel.apache.org/</a></li>
    <li><a href="http://spring.io/blog/2007/12/14/spring-integration-a-new-addition-to-the-spring-portfolio/" target="_blank">http://spring.io/blog/2007/12/14/spring-integration-a-new-addition-to-the-spring-portfolio/</a></li>
    <li><a href="http://java.dzone.com/articles/light-weight-open-source" target="_blank">http://java.dzone.com/articles/light-weight-open-source</a></li>
</ul>
