---
layout: post
title: Il était une fois... Paris Web 2013
date: 2013-10-17 11:58
author: anne b
comments: true
categories: [Evénements, paris web, web]
---
<p style="text-align: justify;"><span style="color: #008000;"><span style="color: #008000;">Paris Web</span></span>, sous-titré webdesign, qualité et accessibilité, c'est un peu le DEVOXX du web, c'est la conférence francophone des gens qui font le web. Elle s'est déroulée du 10 au 12 octobre dernier et s'adressait autant aux webdesigners, intégrateurs web et autres développeurs front.</p>

<p style="text-align: justify;">De ce fait, en tant que développeuse Java et front (entendez par là Javascript et intégration web), je suis autant gourmande de culture web que de culture back ; donc hop! direction le <span style="color: #008000;"><span style="color: #008000;">Palais Brongniart</span> </span>pour deux jours de conférences techniques, ou pas.</p>

<p style="text-align: justify;">Après deux jours de conférences et de multiples montées et descentes d'escaliers (et oui Paris Web c'est aussi bon pour la santé), voici un petit aperçu des conférences qui m'ont le plus marquée.</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><span style="color: #008000;"><strong>* Je code donc je teste... (Cyril Balit)</strong></span></h5>

<p style="text-align: justify;">Un de ces mini talks bien intéressant mais bien trop court. Une présentation succincte mais efficace sur la nécessité des tests et les moyens de les implémenter côté front, justement là où bien qu'émergents, les tests ne font pas encore partie intégrante de la culture web. Mais également un peu frustrante car trop courte pour donner des exemples d'implémentation sur les tests unitaires ou même sur la présentation d'une alternative à Sélénium, qui reste trop procédural pour certain et ne permet pas de faire de tests au niveau du style ou même du design.</p>

<p style="text-align: justify;">En tant que développeuse back, l'écriture de tests s'impose presque comme une évidence. Mais c'est justement parce que je suis déjà sensibilisée à la problématique, qu'en tant que développeuse front l'absence de tests me marque. De bons retours d'expériences et/ou des informations sur les bonnes pratiques sont toujours bons à prendre. Une présentation telle que celle-ci est vraiment une bonne chose, car bien que courte, elle permet de sensibiliser un peu plus la communauté front à la culture du test.</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><strong><span style="color: #008000;">* Soyez responsables : construisez votre API (Matthias Dugué)</span></strong></h5>

<p style="text-align: justify;">Voici une présentation claire sur les bonnes pratiques à avoir pour l'implémentation d'une API, tant au niveau du design que dans l'utilisation des bons outils. On peut noter quelques bonnes pratiques telles que le découplage des éléments, la charte des noms et des usages pour garder de la cohérence, l'utilisation de standards et du versionnement ou encore la bonne gestion des exceptions. Un autre point important est de toujours garder des éléments légers et découplés, arrangés à l'image d'une galaxie, afin de pouvoir les faire évoluer, les remplacer sans avoir à tout casser. Cela vous assurera une API dynamique.</p>

<p style="text-align: justify;">Bref, une autre conférence claire et efficace qu'il est toujours bon de découvrir ou redécouvrir, car les bonnes pratiques disparaissent trop souvent au profit de contraintes projet. ;)</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><strong><span style="color: #008000;">* Goûts et mauvais goût : de la subjectivité en design (Jean-Philippe Cabaroc)</span></strong></h5>

<p style="text-align: justify;">Les goûts et les couleurs, ça ne se discute pas. Certes. Mais tout de même, est-ce qu'un bon design ne peut se résumer qu'à une histoire de goût ? Oui, mais le goût c'est aussi une histoire de mélange : un mélange de cultures, d'époques et des tendances qui les accompagnent (ce qui était tendance dans les années 80 ne l'est par exemple plus aujourd'hui) et aussi d'histoire personnelle.</p>

<p style="text-align: justify;">Des normes esthétiques semblent nous parler au-delà de notre goût : prenez par exemple le nombre d'Or ou le principe de la <span style="color: #008000;"><a title="Psychologie de la forme" href="http://fr.wikipedia.org/wiki/Psychologie_de_la_forme" target="_blank"><span style="color: #008000;">Gestalt</span></a></span>. Mais attention car ces normes ne sont pas magiques et l'esthétisme en design reste également une histoire d'expérience et de sensibilité.</p>

<p style="text-align: justify;">Au-delà du goût et des mathématiques, il y a aussi le contexte : le site d'une hôtellerie de luxe ne sera jamais pensé de la même manière qu'un site vendeur d'électroménagers discount.</p>

<p style="text-align: justify;">Au final, on pourrait ainsi résumer le mauvais goût à ce qui est mal exécuté, en décalage complet avec l'esthétisme de l'époque et le contexte et qui porte atteinte à notre vécu.</p>

<p style="text-align: justify;">Mais attention, car le mauvais goût peut être utilisé de manière consciente et délibérée comme outil de transgression. D'ailleurs, méfiez-vous également de lui, car on ne sait jamais s'il ne sera pas tendance demain. ;)</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><span style="color: #008000;"><strong>* Web Apps et sites web, le jeu des différences (Jérémie Patonnier)</strong></span></h5>

<p style="text-align: justify;">Vinrent AJAX puis le HTML5 et son implémentation dans les navigateurs modernes, qui se voient alors dotés de multiples outils "server-like". Et nous voici aujourd'hui avec des navigateurs qui deviennent de véritables environnements applicatifs. Ceux-ci étant basés sur les maîtrises graphiques (webGL, Canvas, CSS3), des données avec l'implémentation d'indexedDB ou storage, des navigateurs eux-mêmes (mode offline, application cache, history...) ainsi que de l'environnement (géolocalisation, batterie, file API...).
Finalement, avec de tels outils, le schéma classique d'un navigateur qui envoie ses requêtes au serveur va-t-il longtemps perdurer ? N'allons-nous pas de plus en plus déporter le fonctionnel de nos applications au sein même du navigateur, et où ce dernier ne fera plus que des appels à des services "cloudiens" ? Et ainsi arriver à des webapps installables ?</p>

<p style="text-align: justify;">Mais si je déporte tout mon métier dans un navigateur, ne va-t-il pas falloir que je tienne compte des compatibilités multinavigateurs ? Et est-ce vraiment réalisable pour des sites tels qu'Amazon ?
En tout cas, affaire à suivre...</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><span style="color: #008000;"><strong>* Esthétique et pratique du Web qui rouille (Olivier Thereaux et Karl Dubost)</strong></span></h5>

<p style="text-align: justify;">Nous sommes à une époque où nous commençons à appréhender le web, son histoire et ses implications. Aussi justement, que devons-nous faire de notre histoire du web ? Devons-nous absolument tout conserver et archiver ? et ainsi chercher à cristalliser <em>ad vitam eternam</em> nos pages web dans des URLs horodatées ? Ou bien admettre l'impermanence du web ? Nous mettons sans cesse des informations en ligne, mais nous ne savons pas comment les gérer, les conserver ni même quelles sont les attentes des utilisateurs.</p>

<p style="text-align: justify;">Quoiqu'il en soit le web ne cesse d'évoluer, et nous devons faire autant évoluer nos sites web afin de ne pas les laisser rouiller, mais sans pour autant laisser disparaître tout trace de leur passé.</p>

<p style="text-align: justify;">Même si le web fait désormais partie de notre histoire et de notre culture, il faut également savoir accepter son impermanence.</p>

<p style="text-align: justify;">Voici ce qui nous a été présenté. Une de ces conférences qui vous raconte une histoire tout en vous faisant poser de bonnes questions.</p>

<p style="text-align: center;"> •••</p>

<h5 style="text-align: justify;"><span style="color: #008000;"><strong>* Multimodalité &amp; interfaces. Le design est une question d'humains, pas de machine (Geoffrey Dorne)</strong></span></h5>

<p style="text-align: justify;">Aujourd'hui la multiplicité des devices et des moyens d'interactions est bien connue. Nous avons toujours plus d'objets qui s'interposent dans nos moyens de communiquer et partager, mais nous en restons néanmoins des êtres humains. Du coup, l'utilisation de tel moyen plutôt qu'un autre reste principalement une affaire de sentiments et/ou émotions.</p>

<p style="text-align: justify;">Au final la question sociale prime sur la technologie, et une interface définie liée à un device également défini peut se retrouver détournée du contexte initialement prévu. Par exemple, ne vous êtes vous jamais couché avec votre laptop, posé à 90° sur votre matelas, afin de pouvoir regarder votre film en position allongé ? Dans ce cas le laptop se retrouve dans une position détournée de ce qui était initialement prévu.</p>

<p style="text-align: justify;">Au-delà de la technologie pure, les interactions sociales et humaines continuent d'avoir un impact non négligeable sur l'utilisation de telle ou telle technologie, et aujourd'hui il n'est plus possible de penser un nouveau produit sans le penser multimodal. ;)</p>

<p style="text-align: justify;">Beaucoup d'idées et de concepts ont été énoncés dans cette présentation, mais qui vous titillent les neurones.</p>

<p style="text-align: center;"> •••</p>

<p style="text-align: justify;"><span style="line-height: 1.6em;">Même si je suis toujours plus friande de technique, je constate que ce qui me plaît souvent le plus ce sont les présentations qui semblent hors cadres ou un peu marginales. Car elles permettent de penser la technologie au-delà de celle-ci. Elles nous font réfléchir tout en nous apportant de nouvelles visions, idées ou approches. Car au final, aujourd'hui, tous ces devices qui font notre quotidien ne sont pas juste là pour faire des calculs scientifiques, ils font partie intégrante de nos vies et de ce fait se borner à des aspects purement technique n'aboutit en général pas à grand chose. </span></p>
