---
layout: post
title: Montez le niveau de votre gestion des erreurs !
date: 2018-01-16 11:09
author: hadrien mens-pellen
comments: true
categories: [Bonnes pratiques de dév, clean code, code, comparatif, développement, erreurs, exceptions, fonctionnel, java, java8, Programmation]
---
<img class="alignnone" src="https://camo.githubusercontent.com/aa4e64d70659ca54dc52c6c2a5c796d03b65227d/68747470733a2f2f692e696d67666c69702e636f6d2f3178377877312e6a7067" alt="What could go wrong" width="696" height="522" />

&nbsp;

Lors d'une de mes missions avec une architecture classique en couches et sans service de routage, j'ai eu de nombreuses discussions et incompréhensions avec mes collègues quant à la gestion des erreurs. Plutôt que de rester dans le débat théorique, j'ai voulu essayer sur un exemple "classique" toutes les techniques auxquelles je pouvais penser. J'aimerais les partager avec vous pour avoir votre avis et peut-être encore de meilleures solutions. C'est parti !

<h1><a id="user-content-les-specs" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#les-specs"></a>Les specs</h1>

<ul>
    <li>Un utilisateur soumet un formulaire de changement d'adresse.</li>
    <li>Si ce formulaire est valide, sa nouvelle adresse est envoyée au webservice en charge de l'opération.</li>
    <li>L'utilisateur est alors redirigé vers la page de son compte avec un message de succès.</li>
    <li>Si le formulaire est invalide ou que la mise à jour dans le webservice ne fonctionne pas, l'utilisateur est redirigé sur la page de mise à jour de formulaire avec un message décrivant l'erreur. </li>
</ul>

<h1><a id="user-content-le-code" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#le-code"></a>Le code</h1>

<h2><a id="user-content-1-orgie-de-booléens" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#1-orgie-de-booléens"></a>1. Orgie de booléens</h2>

Je commence par le pire, je crois qu'aucune des personnes auxquelles j'en ai parlé n'a considéré ça comme une bonne idée...

<div class="highlight highlight-source-java">
<pre><strong><span class="pl-k">public</span> </strong><span class="pl-smi">Response</span> updateAddress(<span class="pl-smi">Request</span> request) {
<em>    <span class="pl-c">// La requête est une simple collection de paramètres</span></em>
    <span class="pl-smi">UpdateAddressForm</span> form <span class="pl-k">=</span> <strong><span class="pl-k">new</span> </strong><span class="pl-smi">UpdateAddressForm</span>(request);

    <strong><span class="pl-k">if</span> </strong>(form<span class="pl-k">.</span>isValid()) {
        <strong><span class="pl-k">boolean</span> </strong>updateSucceeded <span class="pl-k">=</span> updateUserAddress(form<span class="pl-k">.</span>userId(), form<span class="pl-k">.</span>address());
        <strong><span class="pl-k">if</span> </strong>(updateSucceeded) {
            <strong><span class="pl-k">return</span> </strong>successResponse();
        } <strong><span class="pl-k">else</span> </strong>{
            <strong><span class="pl-k">return</span> </strong>failResponse(<span class="pl-s"><span class="pl-pds">"</span>service unavailable<span class="pl-pds">"</span></span>);
        }
    } <strong><span class="pl-k">else</span> </strong>{
        <strong><span class="pl-k">return</span> </strong>failResponse(form<span class="pl-k">.</span>errors());
    }
}

<em><span class="pl-c">// Je mets la définition de ces méthodes pour plus de clarté, je ne les remettrai pas par la suite.</span></em>
<strong><span class="pl-k">private</span> </strong><span class="pl-smi">Response</span> successResponse() {
    <strong><span class="pl-k">return</span> </strong><span class="pl-smi">ResponseBuilder</span><span class="pl-k">.</span>redirectTo(<span class="pl-smi">AccountPage</span><span class="pl-c1"><span class="pl-k">.</span>URL</span>)
            .withMessage(<span class="pl-s"><span class="pl-pds">"</span>You updated your address, congratulations !<span class="pl-pds">"</span></span>)
            .response();
}

<strong><span class="pl-k">private</span> </strong><span class="pl-smi">Response</span> failResponse(<span class="pl-smi">String</span> errors) {
    <strong><span class="pl-k">return</span> </strong><span class="pl-smi">ResponseBuilder</span><span class="pl-k">.</span>redirectTo(<span class="pl-smi">EditAddressPage</span><span class="pl-c1"><span class="pl-k">.</span>URL</span>)
            .withMessage(<span class="pl-s"><span class="pl-pds">"</span>Sorry your request was denied, it contained the following errors : <span class="pl-pds">"</span></span> <span class="pl-k">+</span> errors)
            .response();
}</pre>
</div>

Quelques problèmes :

<ol>
    <li>Si l'appel à updateUserAddress ne marche pas, on ne sait pas pourquoi.</li>
    <li>Il y a des dépendances temporelles dans le formulaire. On peut récupérer l'identifiant et l'adresse de l'utilisateur même si le formulaire est invalide.</li>
    <li>On a une duplication, certes légère, de code pour le cas en erreur. Dans les spécifications on ne distingue pas les deux cas, pourquoi le faire dans le code ?</li>
    <li>Il y a deux niveaux d'indentation. Ce n'est pas énorme, mais une forte indentation peut nuire à la lisibilité.</li>
</ol>

<h2><a id="user-content-2-error-string" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#2-error-string"></a>2. Error String</h2>

Pour palier au premier problème on peut retourner l'erreur ou les erreurs potentielles. Dans le cas où la méthode doit retourner une valeur, on englobe la valeur dans un objet avec les potentielles erreurs.

<div class="highlight highlight-source-java">
<pre><span class="pl-smi">UpdateAddressForm</span> form <span class="pl-k">=</span> <strong><span class="pl-k">new</span> </strong><span class="pl-smi">UpdateAddressForm</span>(request);

<strong><span class="pl-k">if</span> </strong>(form<span class="pl-k">.</span>isValid()) {
    <span class="pl-smi">String</span> errors <span class="pl-k">=</span> updateUserAddress(form<span class="pl-k">.</span>userId(), form<span class="pl-k">.</span>address());
    <strong><span class="pl-k">if</span> </strong>(errors<span class="pl-k">.</span>isEmpty()) {
        <strong><span class="pl-k">return</span> </strong>successResponse();
    } <strong><span class="pl-k">else</span> </strong>{
        <strong><span class="pl-k">return</span> </strong>failResponse(errors);
    }
} <strong><span class="pl-k">else</span> </strong>{
    <strong><span class="pl-k">return</span> </strong>failResponse(form<span class="pl-k">.</span>errors());
}</pre>
</div>

Maintenant la méthode updateUserAddress retourne des erreurs. Cette ligne n'a pas beaucoup de sens, le but de la méthode étant de faire une action, pas de retourner des erreurs. Je trouve que la signature prête à confusion.

<h2><a id="user-content-3-exception" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#3-exception"></a>3. Exception</h2>

On peut gérer ce problème grâce aux exceptions.

<div class="highlight highlight-source-java">
<pre><span class="pl-smi">UpdateAddressForm</span> form <span class="pl-k">=</span> <strong><span class="pl-k">new</span> </strong><span class="pl-smi">UpdateAddressForm</span>(request);

<strong><span class="pl-k">if</span> </strong>(form<span class="pl-k">.</span>isValid()) {
    <strong><span class="pl-k">try</span> </strong>{
        updateUserAddress(form);
        <strong><span class="pl-k">return</span> </strong>successResponse();
    } <strong><span class="pl-k">catch</span> </strong>(<span class="pl-smi">BusinessException</span> e) {
        <strong><span class="pl-k">return</span> </strong>failResponse(e<span class="pl-k">.</span>errors());
    }
}
<strong><span class="pl-k">return</span> </strong>failResponse(form<span class="pl-k">.</span>errors());</pre>
</div>

On ne récupère plus les erreurs de la méthode en retour normal, mais le code n'est pas beaucoup plus propre.

<h2><a id="user-content-4-plus-dexceptions-" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#4-plus-dexceptions-"></a>4. Plus d'exceptions !</h2>

Il nous reste les trois problèmes suivants à régler :

<ol>
    <li>Il y a des dépendances temporelles dans le formulaire. On peut récupérer l'identifiant et l'adresse de l'utilisateur même si le formulaire est invalide.</li>
    <li>On a une duplication certes légère, de code pour le cas en erreur. Dans les spécifications on ne distingue pas les deux cas, pourquoi le faire dans le code ?</li>
    <li>Il y a deux niveaux d'indentation. Ce n'est pas énorme, mais une forte indentation peut nuire à la lisibilité.</li>
</ol>

Pour tous les régler, on peut lancer une exception à la création du formulaire s'il n'est pas valide.

<div class="highlight highlight-source-java">
<pre><strong><span class="pl-k">try</span> </strong>{
    <span class="pl-smi">UpdateAddressForm</span> form <span class="pl-k">=</span> <span class="pl-smi">UpdateAddressForm</span><span class="pl-k">.</span>tryToCreate(request);
    webService<span class="pl-k">.</span>updateUserAddress(form<span class="pl-k">.</span>userId(), form<span class="pl-k">.</span>address());
    <strong><span class="pl-k">return</span> </strong>successResponse();
} <strong><span class="pl-k">catch</span> </strong>(<span class="pl-smi">BusinessException</span> e) {
    <strong><span class="pl-k">return</span> </strong>failResponse(e);
}</pre>
</div>

<h2><a id="user-content-5-monades" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#5-monades"></a>5. Monades</h2>

La dernière technique à laquelle j'ai pensé est le monade Try. J'ai pris celui de la librairie better-java-monads. L'idée est que Try est une classe abstraite à deux implémentations :

<ul>
    <li>Success, qui contient le résultat de l'opération</li>
    <li>Failure, qui contient l'exception levée en cas d'échec</li>
</ul>

Les méthodes map n'ont d'effet que sur la classe Success. Et la méthode recover renvoie la valeur dans succès ou le résultat de la fonction en paramètre.

<div class="highlight highlight-source-java">
<pre><span class="pl-k">return</span> <span class="pl-smi">UpdateAddressForm</span><span class="pl-k">.</span>from(request) <em><span class="pl-c">// retourne un Try&lt;UpdateAddressForm&gt;</span></em>
        .flatMap(form <span class="pl-k">-</span><span class="pl-k">&gt;</span> updateUserAddress(form<span class="pl-k">.</span>userId(), form<span class="pl-k">.</span>address()))
        .map(<span class="pl-c1">this</span><span class="pl-k">::</span>successResponse)
        .recover(<span class="pl-c1">this</span><span class="pl-k">::</span>failResponse);</pre>
</div>

<h1><a id="user-content-mon-point-de-vue" class="anchor" href="https://gist.github.com/HadrienMP/3f70cf980548d1c54c10408aacb5b224#mon-point-de-vue"></a>Mon point de vue</h1>

<blockquote>"Les opinions c'est comme les <del>(censuré)</del> tout le monde en a un."
<em>Inspecteur Harry</em></blockquote>

Je trouve que la technique avec les monades est très élégante. Cependant, pour le public Java, je la trouve difficilement accessible. Et si une méthode dans une couche profonde renvoie un try, il est probable qu'il faille le faire remonter à travers les couches jusqu'au contrôleur, polluant ainsi toutes les signatures, comme les checked exceptions.

Je préfère la technique des exceptions. Je trouve l'exemple de code tout aussi élégant. De plus, il se trouve que cette technique est très bien alignée avec d'autres idées :

<ul>
    <li>Un objet invalide ne devrait jamais être créé (donc on ne crée pas de formulaire invalide)</li>
    <li>Pour respecter le <a href="https://fr.wikipedia.org/wiki/Principe_de_responsabilit%C3%A9_unique">SRP</a> (Principe de responsabilité unique) une méthode devrait être soit commande soit requête. Les requêtes on un type de retour, les commandes sont void.</li>
</ul>

Pour que cette technique fonctionne à l'échelle d'un logiciel, il faut mettre en place une bonne politique d'exceptions. En général, je ne fais que deux types : BusinessException et TechnicalException. Si une technical exception apparaît dans une appli web, on peut récupérer de l'erreur (rare) ou la laisser remonter. La plupart du temps, en la laissant remonter, on peut facilement diagnostiquer le problème et le corriger. Si une business exception arrive on voudra souvent afficher un message d'erreur à l'utilisateur, une erreur "normale" en quelque sorte.

Ainsi tous les contrôleurs savent qu'ils doivent récupérer une business exception pour afficher les messages d'erreur.

On m'oppose souvent que "les exceptions c'est comme les GOTOs, c'est mal". Ce n'est pas un argument mais un jugement. Et je n'ai jamais subi de dette technique due aux GOTOs. C'est pourquoi...

<h1>Donnez-moi votre avis !</h1>

J'aimerais énormément avoir vos avis en commentaires ou sur Twitter <a href="https://twitter.com/HadrienMP">@HadrienMP</a> !

Si vous n'aimez pas la technique avec les exceptions, j'adorerais avoir l'occasion de changer d'avis !

Quelle technique utilisez-vous d'habitude ?

Quelle est votre technique préférée ?

Tout le code ainsi que les tests sont disponibles sur mon github : <a href="https://github.com/HadrienMP/error-management-styles">https://github.com/HadrienMP/error-management-styles</a>. N'hésitez pas à forker pour me proposer vos améliorations !

P.S. : Si vous voulez une très bonne conférence sur le sujet regardez <a href="http://videos.ncrafts.io/video/221107991">Learning to live with errors</a> par Tomas Petricek!
