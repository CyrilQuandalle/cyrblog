---
layout: post
title: Ecrire du code, un travail rédactionnel comme un autre
date: 2014-11-12 11:04
author: fabien maury
comments: true
categories: [Bonnes pratiques de dév, clean code, développement, Programmation, programming, quality]
---
Le développement logiciel, qui pourtant n'en est pas à sa première décennie d'existence, n'en demeure pas moins une discipline jeune, et on sent que c'est un monde qui se cherche; Beaucoup de débats ouverts, et bon nombre de métaphores pour s’assimiler à des métiers existants de plus longue date: comparaison avec la construction d'un bâtiment, industrialisation du cycle de vie du logiciel, assimilation à la catégorie des artisans (mouvement Software Craftsmanship), etc.
En ce qui me concerne, je considère de plus en plus qu'un des aspects non négligeable de notre métier est la rédaction. Bon nombre de personnes rédigent des documents: que ce soit l'écriture d'un livre, ou plus proche de nous les personnes en charge d'écrire la documentation, les spécifications, les contrats, etc.
De même nous "écrivons" toute la journée, certes dans un style très technique, en langage principalement destiné à la machine, mais certains traits d'exigence du travail rédactionnel s'y retrouvent:

<h2>Être intelligible</h2>

En effet, votre code va être lu par d'autres développeurs, qui voudront corriger, améliorer ou faire évoluer votre code, et pour cela ils devront comprendre ce que vous avez voulu faire.
De la même façon qu'il sera laborieux et fatiguant de lire un livre ou une spécification écrit en langage 'petites annonces' (jh ch. apt prox. com. av balc), il sera préférable d'avoir du <strong>code compréhensible</strong>.

Il faudra donc éviter ce style

[code lang="java"]
public long calcProf(double pa,double t,double pv,double t2) {
return (pv/(1+t))-(pa/(1+t2));
}
[/code]

au profit de celui-çi:

[code lang="java"]
public long calculDuProfit(PrixTTC prixAchat,TVA tvaAchat,PrixTTC prixVente,TVA tvaVente) {
PrixHT prixAchatHT = tvaAchat.reduceFrom(prixAchat);
PrixHT prixVenteHT = tvaVente.reduceFrom(prixVente);
return prixVenteHT.moins(prixAchatHT);
}
[/code]

<h2>Cohérence globale</h2>

Imaginez maintenant que vous écrivez un livre à plusieurs: on vous fait le résumé de l'histoire, chacun prend un chapitre et c'est parti !
Si vous ne communiquez pas, vous risquez d'avoir plusieurs fois le récit de l'attaque du dragon écrit avec des nuances différentes et aucun passage n'expliquant comment les héros sont arrivés là....et si vous ne vous êtes pas mis d'accord sur un style commun le rendu sera perturbant à lire.
Pour le développement c'est pareil ! Il faut une cohérence globale en terme d'architecture de code et de style, afin qu'il soit possible de trouver facilement ce que l'on cherche une fois que l'on connaît un minimum l'application. En résumé: parlez vous. Une équipe qui ne communique pas court à sa perte.
(ce parallèle là est un peu capillotracté...mais je reste persuadé que le fond fait sens)

<h2>Relecture</h2>

Je ne pense pas qu'un seul travail de rédaction soit envoyé pour livraison sans relecture (à part le développement logiciel ... qui est rarement relu).
Aucune maison d'édition n'enverrait un livre à l'imprimerie sans relecture juste pour ne pas froisser l’ego de l'auteur. La plus grande barrière a la relecture du code reste, je pense, un problème de fierté, la relecture s'apparentant à du <em>flicage</em>. Rappelons qu'il n'est pas obligatoire que la relecture se fasse par un supérieur hiérarchique, cela peut se faire entre pairs et dans un bon état d'esprit.

La relecture de code c'est:
- un pas de plus vers la <strong>propriété collective du code</strong>
- un bon moyen de <strong>partager</strong> la connaissance
- avoir un avis extérieur sur son code

<h2>Pensez-y</h2>

Un code difficile à lire provoque une perte de temps, une fatigue intellectuelle et devient source d'erreur.
La compréhension d'un code vient aussi du suivi collectif qui en a été fait avant, via le partage de connaissances et le dialogue au sein de l'équipe.
Donc pensez-y lorsque vous développez, essayez de penser à la prochaine personne qui va venir lire votre code, et essayez donc de lui donner un coup de pouce.
Si vous ne le faite pas pour lui faites-le pour vous, car il y a de grandes chances que le prochain lecteur de ce code soit le vous du futur :-)
