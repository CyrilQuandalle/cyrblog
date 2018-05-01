---
layout: post
title: "NCrafts 2015 : du craftsmanship, du F#, de la passion"
date: 2015-06-01 10:30
author: vincent bourgeois
comments: true
categories: [Actu, DDD, Evénements, event storming, F#, living documentation, NCrafts]
---
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/06/ncrafts-2015.png"><img src="http://www.arolla.fr/blog/wp-content/uploads/2015/06/ncrafts-2015.png" alt="ncrafts-banner-2015" width="513" height="187" class="aligncenter size-full wp-image-4633" /></a>

Ça y est, je suis vraiment entré dans le monde du Software Craftsmanship : j’ai fait mon premier <a href="http://ncrafts.io/" target="_blank">NCrafts les 21 et 22 mai.
</a>
Ce qui m’a frappé en arrivant, c’était le coté intimiste de l’événement : quelques centaines de personnes, deux grandes salles de conférence (la totalité des personnes présentes tenait dans la plus grande !), et une petite salle pour les ateliers.

Le tour des stands est vite fait, on rencontre les grands sponsors de l’événement, ce qui donne un total de huit présentoirs, et autant d’opportunités de manger des bonbons, ou des glaces, ou des barbes à papa pour les plus chanceux (Arolla power !).

Mes commentaires ne sont basés que sur des conférences que j’ai vues, bien évidemment.

<strong>Keynote: The Joy Of Debugging Ourselves par <a href="https://twitter.com/Morendil" target="_blank">Laurent Bossavit</a></strong>

La conférence de départ se concentre sur une introspection du développeur. Celui-ci étant humain, il n’est pas exempt de défauts, d'où la présence de bugs dans le code. Le principe est donc d'apprendre à se connaître et a s'"auto débugger" avant de passer au code. Je pense que beaucoup ont dû rester sceptiques après cette introduction.

<strong>Continuous delivery - the missing parts par <a href="https://twitter.com/stack72" target="_blank">Paul Stack</a></strong>

C’est celle-ci que j’ai vraiment trouvé la plus instructive. Le sujet me tenait à cœur, il s’agissait d’un aspect que j’avais travaillé lors de mes précédentes expériences.

Selon Paul Stack, le continuous delivery n'est pas encore bien maîtrisé par les différents acteurs du logiciel. Il consiste en un certain nombre de règles à respecter autant côté développeur que côté Ops.

Il a cité notamment quelques points qui n'étaient pas souvent respectés côté développeur :

<ul>
<li>Maîtriser son environnement de build</p></li>
<li>Ne générer les binaires qu’une fois pour tous les environnements</p></li>
<li><p>Automatiser au maximum</p></li>
<li><p>Avoir un retour de la production</p></li>
</ul>

<p>Quelques règles supplémentaires également côté ops ne sont pas toujours évidentes :

<ul>
<li>Maîtriser l’environnement de production

</li>
<li>

Pouvoir régénérer l’infrastructure en cas de panne

</li>
<li>

Monitorer au maximum

</li>
<li>

Faire des retours aux Devs

</li>
</ul>

Il a également parlé d’outils et de retours d’expérience. Par exemple, avec cette méthode, le build d'une application a pu passer d'un cycle de plusieurs semaines à quelques minutes, avec un bon point pour la réactivité. Il a également mentionné une démo au cours de laquelle il détruit son environnement de production et le remonte en quelques minutes. En bref, le continuous delivery est une bonne pratique qui allie communication et technique qui contribue à la qualité du SI.

<strong>Interaction Driven Design par <a href="https://twitter.com/sandromancuso" target="_blank">Sandro Mancuso</a></strong>

La problématique ici était de simplifier la compréhension rapide d’un projet par un nouvel arrivant par l’intermédiaire du code.

Il nous parle d’abord de quelques notions de base pour son design :

<ul>
<li>L’organisation du code (packages, namespaces) devrait être regroupée par interactions. Ici, on évite le découpage des namespaces par couche, toutes les couches d’une même interaction sont au même endroit, de toutes façon les conventions de nommage nous permettent déjà d’identifier ces éléments.

</li>
<li>

Les notions métier n’ont de sens que côté « applicatif », le reste c’est de la présentation, de l’interface graphique.

</li>
<li>

On part d’une opération utilisateur (appel à un web service, clic sur un bouton, tout ça regroupé sous le terme « Action »), puis on redescend vers les « domaines métiers » avant d’arriver aux « Dépôts » (bases de données, systèmes de fichier ou tout service n’étant pas unique au métier).

</li>
</ul>

Ces règles simples permettraient donc de facilité la lecture du code du point de vue de son utilisation, et ainsi accélérer la prise en main du code par un nouvel arrivant.

<strong>The Silver Bullet Syndrome par <a href="https://twitter.com/hhariri" target="_blank">Hadi Hariri</a></strong>

Hadi Hariri avait bien prévenu en début de conférence, ici, on apprendra rien, on l’écoutera juste gueuler car apparemment il s’auto proclame « vieux grincheux » (ndlr : traduction édulcorée).

On l’entendra faire l’histoire des différentes tendances informatiques et surtout sur les derniers frameworks JavaScript en clamant que dès qu’une nouveauté pointait le bout de son nez, toute la communauté s’y mettait et délaissait les plus anciens comme de vieux jouets. Il décrit donc le « Silver Bullet Syndrome » comme une suite de nouveautés, sensées faciliter le développement, liées à des phénomènes de mode qui finalement détournent le développeur de sa tâche principale : le développement doit répondre à un besoin et le développeur ne doit pas développer juste pour développer.

J’ai remarqué que la fin de son discours contenait une autre dimension : le recrutement de développeur ne doit pas se faire par les outils utilisés (tout comme on ne choisirait pas son plombier à la marque de son marteau) mais par ses compétences réelles d’adaptation à un environnement et de résolution de problématiques, ce qui devrait pouvoir limiter les effets de ce « Silver Bullet Syndrome ».

<strong>Introducing EventStorming par <a href="https://twitter.com/ziobrando" target="_blank">Alberto Brandolini</a></strong>

L'EventStorming est une nouvelle méthode pour mettre à plat l'ensemble d'un processus par les différents acteurs de celui-ci. La présentation commence par le constat suivant : lors d'un nouveau projet qui nécessite la contribution de plusieurs acteurs, il existe de nombreuses idées préconçues qui nuisent au projet :

<ul>
<li>Il est impossible de réunir tous les acteurs du projet dans une même salle.

</li>
<li>

Si cela arrive, de toute façon ce ne sera pas productif car la communication sera difficile.

</li>
</ul>

L'EventStorming, c'est l'art de mettre à genoux ces deux préjugés. L'idée est très simple : réunir tous les acteurs, leur donner chacun un stylo et des posts-it, et leur demander d'établir ensemble le process dans son intégralité. Pour cela, point de tableau mais une feuille recouvrant la totalité de l'espace pour que chacun puisse s'exprimer. La réunion se fait debout, pour éviter que les acteurs ne s'endorment.

Une fois tous les acteurs mis au courant de leur mission et leur moyen de communication défini, ils se sentent plus impliqués et son plus libres de s'exprimer. C'est aussi l'occasion de repérer les conflits possibles dans le processus et de les régler rapidement. Comme tout le monde travaille en même temps, le processus est généré de manière parallélisée.

<strong>Enterprise Tic-Tac-Toe par <a href="https://twitter.com/ScottWlaschin" target="_blank">Scott Wlaschin</a></strong>

Cette conférence fait partie des nombreuses sur le sujet du F#. Je ne connaissais pas encore le langage. Au cours de cette conférence, Scott Wlaschin nous a montré petit à petit comment développer un Tic-Tac-Toe de manière fonctionnelle.

Pour commencer, il nous a rappelé quelques notions de base de F# qui allaient être les plus utiles tout au cours de sa présentation en live-coding.

Son idée était de réaliser une application client/serveur. Il a commencé par définir les règles du jeu en définissant des types pour chacun des joueurs. Cela faisait un bon écho à la conférence précédente concernant le Type Driven Design.

La partie client était l'occasion pour Scott Wlaschin de présenter le HATEOAS, qui permet de spécifier les actions possibles au run time. Cette conférence est un bon tutorial F#

<strong>If you're not live coding, you're dead coding ! par <a href="https://twitter.com/thinkb4coding" target="_blank">Jérémie Chassaing</a></strong>

La présentation de Jérémie Chassaing commence par le prompt d’une ancienne machine Texas Instrument de 1981. Il s’agit du premier ordinateur que son père lui avait offert pour ses 6 ans. Déjà la nostalgie qui se reflète dans ses yeux met le ton : Jérémie est un passionné !

En quelques minutes, Jérémie nous montre des commandes basiques dont une permettant se faire parler la machine à l’aide d’un synthétiseur vocal intégré… chose possible dès 1981 ! Il nous évoque ensuite sa jeunesse à explorer les possibilités infinies qui lui sont offertes par l’informatique.

Il enchaine sur du F# (tiens, encore !) avec plusieurs exemples en live coding :

<ul>
<li>Commande via Bluetooth d'un robot

</li>
<li>

Lecture de "Au clair de la lune"

</li>
<li>

Utiliser le synthétiseur vocal pour compter de 1 à 20 en remplaçant certains chiffres

</li>
</ul>

Bref, ce passionné saura transmettre son engouement pour le F#, et m'aura donné envie d'essayer.

<strong>When DDD meets Documentation par <a href="https://twitter.com/cyriux" target="_blank">Cyrille Martraire</a></strong>

De loin la meilleure conférence de toutes. Blague à part, j’avais participé à une preview le lundi précédent donc je connaissais un peu le sujet. Cyrille nous a fait comprendre que la documentation ne devait pas être la cinquième roue du carrosse pour le développeur. Pour cela, il remonte au principe même de la documentation : la transmission du savoir. Elle n’est donc pas forcément sous forme d’un document Word. Pour être efficace, une documentation doit répondre à un besoin : que ce soit expliquer des termes techniques ou des process métiers à un nouvel arrivant.

La documentation est un ensemble. Dans le cas d’un nouvel arrivant, il est possible de lui apprendre le métier en le plongeant directement dans le milieu (le faire travailler dans une centrale de distribution par exemple). Il est possible également de garder un œil sur le cœur de métier en maintenant un tableau « investigation board » comme dans les séries policières américaines. Le code doit pouvoir être auto-documenté, et le pair programming doit permettre de transmettre encore plus de savoir (la transmission de pair à pair est de loin la plus efficace, car un dialogue sous forme de questions/réponses se crée).

Il ne faut cependant pas oublier la documentation « papier », mais comment rendre celle-ci attractive pour le développeur ? Ici, Cyrille se rapproche du DDD et mentionne les bounded context, Entities, Value object, etc. Toutes ces notions peuvent par exemple être mentionnés par des attributs et un script peut automatiser la génération de la documentation à partir du code en se basant dessus. Ce n’est pas limité à de l’explication textuelle, mais cela peut s’ouvrir à des diagrammes (par défaut : de classe). Cela garde un aspect ludique pour le développeur, et permet d’avoir une documentation qui « vit » en même temps que le code.

Pour aller plus loin : une fois la mise en place de ces diagrammes automatiques, il est possible de vérifier la qualité du code. En effet, si par exemple on reprend le diagramme de classes, on peut vérifier que l’architecture hexagonale qu’on a voulu mettre en place possède bien ses dépendances dans le bon sens. La documentation ne devient plus un boulet, elle devient une part essentielle du développement.

&nbsp;
&nbsp;
&nbsp;

<p style="text-align: justify;">Quelques liens pour en savoir plus:</p>

<a href="https://fr.wikipedia.org/wiki/Software_craftsmanship" target="_blank">https://fr.wikipedia.org/wiki/Software_craftsmanship</a>

<a href="http://manifesto.softwarecraftsmanship.org/" target="_blank">http://manifesto.softwarecraftsmanship.org/</a>

<a href="http://www.meetup.com/fr/paris-software-craftsmanship/" target="_blank">http://www.meetup.com/fr/paris-software-craftsmanship/</a>

<a href="http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862" target="_blank">http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862</a>
