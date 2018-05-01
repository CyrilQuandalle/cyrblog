---
layout: post
title: Tous les acteurs d'un projet sont entre une chaise et un clavier
date: 2014-07-21 10:55
author: nicolas fedou
comments: true
categories: [Agilité, Bonnes pratiques de dév]
---
<p style="text-align: justify;">En fait, tous les acteurs d'un projet sont capables de mal comprendre, mal exprimer une idée, que ce soit l'idée d'un besoin ou l'idée d'une réalisation...
Ainsi, nous pouvons TOUS être responsables d'un écart entre le logiciel qu'il faut et le logiciel qui est fourni.
Les acteurs varient d'un projet à l'autre. Il y a pourtant une série de rôles qui reviennent tout le temps : l'utilisateur (ou client), l'analyste (ou PO), chef d'équipe (PMO), le programmeur (dev), l'intégrateur logiciel (devops), le testeur (QA). Chacun de ces profils peut se tromper, faire un bon choix à l'instant T qui se révélera inadapté à T+2. Les impacts de chaque rôle sur le logiciel final ne concernent pas que le code, les bugs ne se limitent pas au code et nos méthodes et pratiques (XP, Agile, BDD, ...) ne concernent pas que le code.</p>

<p style="text-align: justify;">La qualité d'un logiciel ne dépend pas que du code mais du projet dans son ensemble. Parlons un peu de la qualité d'un projet :</p>

<p style="text-align: justify;"><strong>La qualité d'un projet</strong> se constate avec la satisfaction des utilisateurs vis à vis de la solution qu'on leur fournit (et non vis à vis de leur demande initiale). Ce constat est qualitatif et fait généralement consensus. Il se commente, mais ne se mesure pas souvent, les utilisateurs pouvant être des clients, des partenaires ou des employés de la direction d'à côté (RH, finance, achat, logistique, ...). On peut mesurer les ventes faites aux clients, mais pour les applications métiers internes, cela se complique. En même temps, cette qualité de projet ne regarde que l'extérieur du logiciel et ne présage rien du futur...</p>

<h1 style="text-align: justify;">Une métrique seule pour un projet complet</h1>

<p style="text-align: justify;">J'ai hésité sur ce titre avec *une utopie pour un manager...*</p>

<p style="text-align: justify;">Un second critère de qualité est la capacité du projet à corriger les écarts entre ce que l'on fournit et ce qui est voulu. Cela se traduit par la capacité à corriger des bugs, mais aussi à livrer des évolutions. Sur ce thème, nous avons la possibilité de mettre des mesures en place. En effet, les bugs et autres évolutions réunissent quelques caractéristiques intéressantes pour les managers :</p>

<p style="text-align: justify;">1. Ils sont visibles (un calcul est devenu correct, une nouvelle information apparaît sur l'écran).</p>

<p style="text-align: justify;">2. Ils ont des critères de fin (cf "definition of done"): ils sont acceptés par des testeurs, ils sont documentés, etc.</p>

<p style="text-align: justify;">3. Ils ont un délai mesurable entre leur identification et leur disparition (cf livraison en prod).</p>

<p style="text-align: justify;">Pour ce point 3, la disparation a lieu à la mise en production. Comme nous livrons des lots de modifications en production, les premières corrections attendent les dernières avant d'être mises en production. Généralement, le travail de mise en production d'une version dépend très peu du nombre de modifications à livrer... Par contre, on teste et on déclare les modifications closes une par une en recette. On aura un délai plus personnalisé pour chaque modification.</p>

Ce délai est quantitatif et concret, mais pas objectif. Il est le résultat du travail de beaucoup de personnes et de contextes de travail très variables d'un projet à l'autre. Ce délai a d'ailleurs une telle dépendance au projet qu'il ne permet pas de comparer un projet à un autre... Par contre, cet indicateur est révélateur de la santé d'un projet !

<p style="text-align: justify;">Si un temps moyen de livraison de modifications montre une santé ou des symptômes, il ne suffit pas à l'obtention d'un diagnostic. Par contre, si on regarde comment ce temps est construit, on pourra en tirer des enseignements...</p>

<p style="text-align: justify;">Ce temps moyen de modifications devrait s'allonger au fur et à mesure que le logiciel s'enrichit de fonctionnalités, se complexifie, grossit en termes de quantité de code à lire et à comprendre... Et ce jusqu'à un point de non rentabilité où le coût du changement est plus élevé que le budget que l'on a pour le faire. Nous parlons de dette technique pour décrire cette augmentation.</p>

<p style="text-align: justify;">Cette mesure peut aussi stagner ou se réduire en ayant pas ou peu de dette, en utilisant une organisation plus efficace, etc...</p>

<h1 style="text-align: justify;">Un ensemble de mesures hétérogènes suivant un seul objectif</h1>

<p style="text-align: justify;">On arrive à la partie utopique quand on souhaite prendre les mesures : qui a un outil de suivi d'activités qui prend en compte les temps de travail de chaque acteur du projet, des aller-retour de chaque tâche/fonctionnalité/user story ?
Autant nous avons un outil de suivi des développements que l'on peut étendre aux testeurs, mais pour l'intégration et les analystes (PO, ...) ?</p>

<p style="text-align: justify;">On voit régulièrement des organisations qui mettent en place de nouveaux process avec de nouvelles contraintes pour saisir les temps de travail, par exemple. Il vaut mieux essayer de rester proches des méthodes de travail actuelles en passant sur des outils de saisie de travail qui permettent une surveillance par les logs et donc des statistiques sur le temps passé sur chaque bug ou évolution.</p>

<p style="text-align: justify;">Ainsi, les outils de bug tracking sont bien pratiques, car l'état des demandes et leurs historiques sont souvent accessibles dans l'outil.
Il n'y a plus qu'à éditer les spécifications et autres recueils de besoins dans un outil collaboratif tel qu'un wiki. Les rapports de campagne de tests ? idem, ...</p>

<p style="text-align: justify;">Nous avons vu que le temps moyen seul indique un état de santé, un résultat d'une organisation du travail. Cette mesure seule ne nous aide pas à trouver des leviers pour réduire notre coût de fonctionnement.</p>

<p style="text-align: justify;">C'est finalement en regardant et en explorant les temps de travail de chaque acteur, les temps d'attente entre chaque étape et en reconstituant l'historique de chaque fonctionnalité (aller-retour entre analyste et développeur, etc...) que l'on va voir émerger les dysfonctionnements.</p>

<p style="text-align: justify;">Si il y a de nombreuses versions d'une fonction de la part des analystes dues a des retours des développeurs et des testeurs, il serait peut-être bon de réunir ces trois amigos que sont les analystes, développeurs et testeurs pour qu'ils transforment la spécification en contrat compris de tous tel que le préconise le bdd.</p>

<p style="text-align: justify;">Si les développeurs reprennent souvent leurs IHM à cause de retours des testeurs et utilisateurs, il serait bon qu'ils aient une livraison continue de leurs commit sur une plateforme de développeurs pour valider leurs choix au plus tôt.</p>

<p style="text-align: justify;">Si les temps de tests s'allongent, il faut peut-être automatiser les campagnes de non régression...</p>

<p style="text-align: justify;">Si les allers-retours sont trop nombreux, trop fréquents, il serait sûrement rentable de passer à l'agile : qui prône la communication entre collaborateur avant l'usage d'outils.</p>

<h1 style="text-align: justify;">Un sous ensemble de mesures pour justifier un retour sur investissement</h1>

<p style="text-align: justify;">On entend parfois, que c'est le développeur qui commite le code, donc il a le pouvoir, notamment le pouvoir de choisir sa façon de travailler... Sauf qu'on a des délais à tenir et que cette idée ne concerne que l'organisation personnelle.</p>

<p style="text-align: justify;">Par exemple, on surestime toujours pour s'assurer contre une mauvaise surprise, s'il n'y a pas cette mauvaise surprise, on a un peu de temps pour essayer une nouvelle pratique. Entre notre temps de passionné et notre temps économisé au travail on peut finir par faire du TDD dans son coin. On finit en montrant l'exemple à l'équipe qui peut finir par faire du TDD.</p>

<p style="text-align: justify;">J'ai vu des équipes de développement qui font de l'agile dans leur coin. Ils ont le vocabulaire et parfois quelques cérémonies, mais pas les acteurs du projet ni l'appui de ceux qui définissent l'organisation de l'entreprise : les managers.</p>

<p style="text-align: justify;">Pour passer cette étape, il vous faut justifier, ou montrer les gains attendus et s'il n'y a pas de mesures concrètes, il n'y a que des promesses. Du coup, quand une pratique vous plaît, trouvez des alliés chez vos collègues, mais aussi mesurez le temps de travail de l'équipe avant et après la mise en place de la nouvelle pratique. Cela montrera le coût de possession de la pratique. Aller chercher les gains de cette pratique et mesurez les aussi. Une évolution sur du code testé doit être plus rapide qu'une évolution sur code legacy... Enfin, mesurer le coût de ne pas avoir cette pratique : sur un problème de production qui n'aurait pas eu lieu avec une campagne de non régression systématique (automatisée ET lancée régulièrement). Ce dernier point se voit comme une assurance, on investit pour ne pas avoir de coût de réparation.</p>

<p style="text-align: justify;">On parle souvent de bonnes pratiques aux développeurs. Il faudrait de temps en temps parler de temps de travail aux managers...</p>
