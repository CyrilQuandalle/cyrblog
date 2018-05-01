---
layout: post
title: Retour sur la soirée Paris Software Craftsmanship du 25 octobre 2012
date: 2012-10-29 19:14
author: christophe michel
comments: true
categories: [Actu, Bonnes pratiques de dév, code legacy, Evénements, software craftsmanship]
---
<p style="text-align: justify;">Nous étions réunis jeudi soir pour une session pratique sur le code legacy présentée par <a href="http://www.meetup.com/paris-software-craftsmanship/member/45041742/">Mathieu Gandin</a>, développeur et coach agile, qui venait partager son expérience sur le sujet.</p>

<p style="text-align: justify;">La présentation s’engage avec <a href="http://www.meetup.com/paris-software-craftsmanship/member/27887802/">Stéphane Bagnier</a>  sur un constat : la plupart du temps, les bonnes pratiques logicielles associées au software craftsmanship (e.g. le TDD) sont souvent expliquées dans un contexte de code neuf. Pourtant, nous savons tous d’expérience que nous finissons inévitablement par devoir travailler sur du code legacy. Dès lors, comment peut-on adapter ces principes au code legacy pour en tirer tous les bénéfices ?</p>

<p style="text-align: justify;">Après une définition de de ce qui caractérise le code legacy (âge, techno, qualité, …), Mathieu nous rappelle qu’il crée généralement de la valeur pour le client (indépendemment de la dette technique qu’il peut porter), qu’il est donc important de préserver cette valeur.</p>

<p style="text-align: justify;">C’est là qu’entrent en jeu les tests, qui vont permettre d’éviter les régressions tout en travaillant le code legacy. Problème : ce code est souvent difficile à tester pour de nombreuses raisons :</p>

<ul style="text-align: justify;">
    <li>dépendances codées en dur</li>
    <li>code procédural trop long et trop complexe</li>
    <li>god objects</li>
    <li>…</li>
</ul>

<p style="text-align: justify;">Difficile de refactorer sans tester, mais dans le cas présent, impossible de tester sans refactorer. Il faut donc procéder par étapes simples :</p>

<ol style="text-align: justify;">
    <li>identifier le point d’entrée de la fonctionnalité sur laquelle on intervient</li>
    <li>écrire un premier test autour de ce point d’entrée tout en refactorant le strict nécessaire</li>
    <li>voir le test échouer</li>
    <li>écrire des tests supplémentaires pour la non-régression</li>
    <li>corriger l’anomalie/implémenter l’évolution</li>
</ol>

<p style="text-align: justify;">Cette approche par refactorings légers permet d’avoir du code de moins en moins mauvais, et de rapidement bénéficier de l’apport des tests pour à la fois garantir la qualité des modifications apportées au code legacy et de documenter le comportement du code legacy (dont la documentation justement est rarement l’une des qualités premières).</p>

<p style="text-align: justify;">Mathieu termine en rappelant que pour certaines équipes, travailler sur du legacy peut être l’occasion de revoir le rapport des développeurs à leur code et d’en transférer la propriété à l’équipe entière, ce qui facilite des revues de code apaisées (moins de problèmes d’ego).</p>

<p style="text-align: justify;">Vient ensuite le temps de mettre en pratique ces principes sur un cas relativement simple, celui d’un composant de planification (Scheduler), qui sert à la planification de réunions dans une entreprise.</p>

<p style="text-align: justify;">Des binômes se forment et nous aurons plusieurs interventions à faire sur le code (Java ou C#) pour y corriger deux anomalies tout en garantissant le fonctionnement actuel.</p>

<p style="text-align: justify;">La première anomalie est celle qui aura donné le plus de fil à retordre à tous les groupes : en cause, les dépendances du Scheduler, l’une envers un composant d’envoi d’emails et l’autre envers un composant d’écriture dans une console. Elles rendaient en effet l’objet impossible à tester : la configuration du composant email, à laquelle nous n’avons pas accès, est invalide pour notre environnement de test (le serveur mail est inaccessible), quant au composant console, il interrompt l’exécution du test pendant plus d’1m30 à chaque exécution.</p>

<p style="text-align: justify;">public class Scheduler {
private String owner = "";
private MailService mailService;
private SchedulerDisplay display;
private List events = new ArrayList();</p>

<p style="text-align: justify;">public Scheduler(String owner) {
this.owner = owner;</p>

<p style="text-align: justify;">mailService = MailService.getInstance();
display = new SchedulerDisplay();
}
/* … */
}</p>

<p style="text-align: justify;">Tous les groupes ont alors procédé à l’externalisation de ces deux dépendances, lesquelles ont été mockées à la main (via un refactoring supplémentaire et la création d’interfaces) ou via un framework ad hoc.</p>

<p style="text-align: justify;">Il aura fallu plus des ⅔ du temps imparti pour écrire le premier test en échec, le travail devenant beaucoup plus simple par la suite. Les mêmes techniques et principes ont ensuite été appliqués de nouveau pour les anomalies suivantes, plusieurs groupes venant présenter leur travail.</p>

<p style="text-align: justify;">L’écriture des tests et le travail en binôme ont également permis, comme c’est le cas habituellement, de mettre en évidence des incertitudes dans la spécification et des problèmes de responsabilités entre les différents objets.</p>

<p style="text-align: justify;">Pour ceux qui voudraient refaire l’exercice chez eux :</p>

<ul style="text-align: justify;">
    <li><a href="http://www.slideshare.net/Mgandin/working-effectively-with-legacy-code-14894858">Slides de la présentation de Mathieu Gandin</a></li>
    <li><a href="https://github.com/octomga/meetupswc">Code du projet d’exemple sur github</a></li>
</ul>

<p style="text-align: justify;">Si la complexité de l’exercice était forcément limitée par les contraintes de temps (classes simples, peu de dépendances), contrairement au code legacy qu’on rencontre dans nos projets, il a permis de s’approprier efficacement la méthode exposée en début de soirée.</p>

<p style="text-align: justify;">Ceux qui sont intéressés par la problématique du legacy pourront lire le classique Working Effectively with Legacy Code de Michael Feathers, dont s’est notamment inspiré Mathieu pour sa présentation.</p>

&nbsp;
&nbsp;
&nbsp;

<p style="text-align: justify;">Quelques liens pour en savoir plus:</p>

<a href="https://fr.wikipedia.org/wiki/Software_craftsmanship" target="_blank">https://fr.wikipedia.org/wiki/Software_craftsmanship</a>

<a href="http://manifesto.softwarecraftsmanship.org/" target="_blank">http://manifesto.softwarecraftsmanship.org/</a>

<a href="http://www.meetup.com/fr/paris-software-craftsmanship/" target="_blank">http://www.meetup.com/fr/paris-software-craftsmanship/</a>

<a href="http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862" target="_blank">http://www.amazon.com/Software-Craftsmanship-The-New-Imperative/dp/0201733862</a>
