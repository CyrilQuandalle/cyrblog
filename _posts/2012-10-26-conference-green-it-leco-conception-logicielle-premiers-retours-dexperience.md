---
layout: post
title: "Conférence Green IT - l'éco-conception logicielle: premiers retours d'expérience"
date: 2012-10-26 15:31
author: Aude Amarrurtu
comments: true
categories: [Actu, éco-conception, Evénements, green IT, green patterns]
---
<p style="text-align: justify;">Jeudi 25 octobre avait lieu à l'EPITA une conférence sur le thème "<a href="http://www.epita.fr/conference-Green-IT-2012.aspx" target="_blank">Green IT - l'éco-conception logicielle: premiers retours d'expérience</a>". Et, bien sûr, Arolla y était!</p>

<h1 style="text-align: justify;">Contexte général de l'événement</h1>

<p style="text-align: justify;">La conférence était organisée à l'EPITA et orchestrée de main de maître par <a href="http://www.transitions2.net/profile/fbordage" target="_blank">Frédéric Bordage, expert Green IT</a>. Même si l'événement était ouvert à tous, le public se composait majoritairement d'étudiants, de rares professionnels s'étaient déplacés. L'audience comptait néanmoins une bonne centaine personnes.</p>

<p style="text-align: justify;">Première constatation, le public était extrêmement attentif et les questions fusaient. Il ne s'agissait pas d'une simple curiosité de la part de ces étudiants mais d'une réelle préoccupation, contrastant de ce fait avec la très faible représentativité des entreprises dans l'assistance. Ce décalage sera de plus en plus visible au fil de l'après-midi: on a ainsi pu constater que les étudiants étaient globalement très optimistes en ce qui concerne l'engagement écologique des entreprises au quotidien, alors qu'il faut bien admettre que, dans les faits, le Green reste très marginal dans les pratiques professionnelles, en particulier dans le monde de l'informatique.</p>

<p style="text-align: justify;">Face à ce public enthousiaste, des intervenants tout aussi passionnés ont présenté leur vision du Green IT et de l'éco-conception logicielle, ne laissant aucun temps mort et rendant les échanges très riches et denses.</p>

<h1 style="text-align: justify;">Une question de bon sens...</h1>

<p style="text-align: justify;">Ce qui ressort largement des différentes présentations, c'est qu'intégrer le Green dans la conception logicielle relève bien souvent du simple bon sens...</p>

<p style="text-align: justify;">Frédéric Bordage, lors de son introduction, s'est attaché à présenter brièvement différents retours d'expérience (les exemples d'IBM, Microsoft et Facebook, entre autres, ont pu être cités), révélant ainsi</p>

<ul style="text-align: justify;">
    <li>qu'il est possible de changer ses comportements à plusieurs étapes de la conception logicielle (lors des spécifications fonctionnelles, du développement en tant que tel, de la maintenance...)</li>
    <li>que certaines décisions peuvent avoir des impacts extrêmement forts (exemple de Facebook qui, en changeant la compilation de son code, a divisé par deux ses besoins de serveurs).</li>
</ul>

<p style="text-align: justify;">Notons également que limiter son empreinte écologique peut se manifester de différentes façons:</p>

<ul style="text-align: justify;">
    <li>limiter la consommation énergétique d'un équipement</li>
    <li>"alléger" une application (et donc la consommation énergétique qu'elle entraîne)</li>
    <li>prolonger la durée de vie d'un équipement ou d'une application</li>
    <li>optimiser l'utilisation d'un équitement</li>
    <li>réduire le temps de maintenance d'une application (et réduire du même fait les ressources nécessaires)</li>
</ul>

<p style="text-align: justify;">On en conviendra, rien de révolutionnaire, il s'agit avant tout d'une prise de conscience.</p>

<h1 style="text-align: justify;">Analyser chaque étape de la conception logicielle</h1>

<p style="text-align: justify;">Ainsi qu'<a href="http://www.blueight.com/" target="_blank">Erwan Bouvier, co-fondateur de la société de conseil en Green IT Blueight</a>, l'a clairement exprimé, chaque étape de la conception logicielle peut être optimisée dans une démarche Green.</p>

<p style="text-align: justify;">A commencer par les spécifications fonctionnelles: en moyenne, 45% des fonctionnalités demandées ne sont pas utilisées! D'ailleurs, on estime que seules 10 à 20% des fonctionnalités de Word sont réellement utilisées... d'où l'importance d'un cahier des charges vraiment réfléchi qui permette d'économiser des ressources dans le cadre du développement et peut-être lors de l'utilisation de l'application.</p>

<p style="text-align: justify;">Une bonne urbanisation peut être génératrice d'économie de temps, de moyens et d'énergie: 10 à 50% des applications seraient redondantes dans les grandes entreprises...</p>

<p style="text-align: justify;">La phase de développement peut également être écolo-optimisée, dans la mesure où l'empilement des frameworks et des composants, s'il permet de réduire le temps passé à développer, est très gourmand en termes de consommation énergétique. En outre, bien documenter son application permettra d'optimiser la maintenance, il s'agit d'adopter des méthodes de travail durables.</p>

<p style="text-align: justify;">La phase d'utilisation, ou de vie, de l'application, est bien sûr la plus longue, il ne faut donc pas la négliger... ni la fin de vie de l'application: 25% des applications ne seraient plus utilisées depuis 3 ans mais continueraient à exister, avec tous les impacts que l'on peut imaginer.</p>

<p style="text-align: justify;">Ayons donc quelques réflexes: réutiliser les ressources existantes (bases de données, équipements, applications...), optimiser les nouvelles ressources, définir des solutions durables (choisir par exemple des langages d'avenir et non vieillissants), penser à la virtualisation!</p>

<h1 style="text-align: justify;">Focus sur le projet Code Vert</h1>

<p style="text-align: justify;">Thierry Leboucq est venu présenter le projet Code Vert sur lequel il travaille. L'idée est simple mais intéressante: développer un outil d'évaluation qui permettre de détecter dans le code les patterns consommants (ou "anti green patterns").</p>

<p style="text-align: justify;">Depuis février, plusieurs sociétés et un laboratoire universitaire collaborent, avec pour objectif de livrer un outil en mode SaaS d'ici dix-huit mois.</p>

<p style="text-align: justify;">Pour mettre en place cet outil, les contributeurs sont partis d'un référentiel d'une centaine de règles issues du bon sens et censées réduire la consommation énergétique d'une application. Ces règles ont été notamment récoltées grâce à une abondante recherche bibliographique.</p>

<p style="text-align: justify;">La réelle difficulté du projet est la mesure qui permettra d'établir si oui ou non ces règles ont un impact écologique positif. Pour ce faire, la laboratoire a mis en place différents outils, des wattmètres bien sûr, mais également des outils plus théoriques (comme PowerAPI ou Qualitune). Les indicateurs étudiés sont le temps d'exécution de l'application et l'énergie consommée. Les essais en laboratoire permettent aussi d'étudier les impacts réels des corrections, en comparant les mesures effectuées avant et après les corrections.</p>

<p style="text-align: justify;">A terme, il est également envisagé de mettre en place des corrections de code automatisées pour éliminer certains anti green patterns. Il est également question de créer, en plus de l'outil de diagnostic, un outil d'accompagnement au développement pas à pas, pour intégrer dès le départ les green patterns, et pas <em>a posteriori</em>.</p>

<p style="text-align: justify;">Un projet encore à ses débuts mais intéressant et à suivre...</p>

<h1 style="text-align: justify;">En enfin...</h1>

<p style="text-align: justify;">S'en est suivie une autre intervention sur un outil d'aide au développement des applications web basé sur des green patterns et pensé pour éviter toute surconsommation. Enfin, une table ronde a ponctué l'après-midi: elle a été l'occasion des nombreuses questions et de réflexions riches grâce aux étudiants et aux intervenants.</p>

<p style="text-align: justify;">Une conférence intéressante et prometteuse, qui a permis de vérifier qu'il reste encore beaucoup à faire mais que la jeune génération est pour le moins enthousiaste!</p>
