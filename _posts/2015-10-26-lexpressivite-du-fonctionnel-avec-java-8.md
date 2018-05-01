---
layout: post
title: L'expressivité du fonctionnel avec Java 8
date: 2015-10-26 11:52
author: raphael squelbut
comments: true
categories: [Bonnes pratiques de dév, Fonctionnel, Java 8, java8, Programmation]
---
<h2>Contexte</h2>

Dans ma dernière mission chez un fournisseur de voyage, l'application a été migrée en Java 8.

Pour moi qui utilise Guava depuis quelques années (depuis <a href="http://minilien.fr/a08em1">cet article de Cyrille Martraire</a> en fait), c'est une excellente nouvelle. 
J'avais constaté qu'en de multiples endroits, on parcourait des listes, puis en fonction de l'item, on remplissait une nouvelle liste, ou alors on filtrait, etc .... Bref, une sorte d'énorme bac à sable pour tester la programmation fonctionnelle et son expressivité. J'étais ravi.

Et un jour ... en cherchant l'origine d'un bug ... je tombe sur ce bout de code :

[java]
/**
* Compute how many chunks to delete for
* given Travel to insert in database.
* The number of chunks is mapped with fares to exclude from billing
*/
private int compute(final Travel travel) {
    int chunksToRemove = 0;
    final Price[] prices = travel.getPrices();
    if (!ArrayUtils.isEmpty(prices)) {
        Arrays.asList(prices);
        for (final Price price : prices) {
            final FarePolicy[] farePolicies = price.getFarePolicies();
            final CustomerFare[] customerFares = price.getCustomerFares();
            for (final FarePolicy fareCalculation : farePolicies) {
                for (final String excludedFare : billing.getExcludedFares()) {
                    if (excludedFare.equals(fareCalculation.getFareName())) {
                        for (final CustomerFare customerFare : customerFares) {
                            if (customerFare.getType().equals(
                                fareCalculation.getPassengerType())) {
                                chunksToRemove +=
                                        (fareCalculation.getChunks().length
                                        * customerFare.getCustomersQuantity());
                            }
                        }
                    }
                }
            }
        }
    }
    return chunksToRemove;
}
[/java]
Bon là, je crois que c'est urgent, il faut refactorer !!!
Avant de modifier le code, commençons par une rapide analyse du code.

<h3>Premiers constats</h3>

<ul>
<li>Aïe, on est dans une méthode privée.</li>
<li><code>Arrays.asList(prices);</code> ne sert à rien</li>
<li>Si <code>billing.getExcludedFares()</code> est null, alors on aura une NullPointerException dans notre boucle <code>for</code>. C'est d'ailleurs le bug qui m'a amené ici. (Ce n'est pas le cas pour <code>farePolicies</code> et <code>customerFares</code> car l'API qui nous fournit ces objets nous assure que ces tableaux ne seront jamais nuls)</li>
<li>Le commentaire et le nom de la méthode ne sont pas d'une grande aide pour comprendre ce que fait cette méthode.</li>
</ul>

Cette méthode est appelée lors de l'application d'un tarif sur un voyage. Elle calcule un nombre utilisé lors de l'application du tarif. Son importance et sa complexité justifient selon moi la création d'un objet dédié.

Pour pouvoir commencer à refactorer, je dois pouvoir être capable de tester simplement le code existant, afin d'être sûr de ne pas introduire de régression. Or l'intention de cette méthode privée est "testée" via sa méthode appelante. Cool j'ai un jeu de test :+1:. Je vais donc l'extraire et l'utiliser pour tester mon nouvel objet.

<h3>Premières corrections</h3>

<ul>
<li>Créer une classe propre pour cette méthode et isoler le test.</li>
<li>Supprimer le code inutile</li>
<li>Vérifier au début de la méthode si toutes mes données sont cohérentes avant de continuer le calcul.</li>
<li>Supprimer le commentaire et trouver un nom adéquat quand j'aurais compris son utilité/fonctionnement</li>
<li>Extraire une méthode qui va effectuer le calcul pour un <code>Price</code></li>
</ul>

Et ça donne ça :

[java]
public int compute(final Travel travel) {
    if (!acceptsPreconditions(travel)) {
        return DEFAULT;
    }

    final Price[] prices = travel.getPrices();
    int chunkToExclude = DEFAULT;
    for (final Price price : prices) {
        chunkToExclude += countExclusionsForPrice( price);
    }
    return chunkToExclude;
}

public int countExclusionsForPrice(Price price) {
    int retour = DEFAULT;
    final FarePolicy[] farePolicies = price.getFarePolicies();
    final CustomerFare[] customerFares = price.getCustomerFares();
    for (final FarePolicy farePolicy : farePolicies) {
        for (final FareLabel excludedFare : billing.getExcludedFares()) {
            if (excludedFare.equals(farePolicy.getFareName())) {
                for (final CustomerFare customerFare : customerFares) {
                    if (customerFare.getType().equals(farePolicy.getCustomerType())) {
                        retour += 
                               farePolicy.getChunks().length 
                               * customerFare.getCustomersQuantity();
                    }
                }
            }
        }
    }
    return retour;
}
[/java]
Avec une constante <code>DEFAULT = 0</code> et une méthode <code>acceptPreconditions(...)</code> qui vérifie que le Travel passé en paramètre est bon mais aussi que <code>billing</code> est dans un état cohérent avec le calcul. Cette méthode pourra être enrichie avec toutes les futures éventuelles vérifications préalables.

Voilà, c'est un peu mieux mais sans programmation fonctionnelle, on peut difficilement aller plus loin. On ne peut pas séparer les deux boucles imbriquées. C'est dommage pour la lisibilité du code.

On peut maintenant rentrer dans la phase 2 et essayer de comprendre en profondeur ce code.

<h3>Deuxième phase</h3>

<ul>
<li>On a un <code>Travel</code> qui contient des <code>Prices</code></li>
<li>Chaque <code>Price</code> contient 

<ul>
<li>des <code>FarePolicy</code> </li>
<li>des <code>CustomerFare</code></li>
</ul></li>
<li>Chaque <code>FarePolicy</code> contient

<ul>
<li>un <code>FareLabel</code></li>
<li>un <code>CustomerType</code></li>
<li>les <code>Chunk</code>s (sorte de bout de trajet) du <code>Travel</code> associés </li>
</ul></li>
<li>Chaque <code>CustomerFare</code> contient

<ul>
<li>un <code>CustomerType</code></li>
<li>le nombre de passagers du <code>Travel</code> associés</li>
</ul></li>
<li>On a un <code>billing</code> qui retourne les <code>FareLabel</code> exclus. </li>
</ul>

Lorsque l'on calcule le tarif global du <code>Travel</code>, on applique un tarif à chaque morceau de trajet (<code>Chunk</code>). 
Le nombre de <code>Chunk</code> sur lesquels on applique un tarif est multiplié par le nombre de passagers  concernés par cette politique tarifaire.

Pour avoir le tarif total, il ne faut pas comptabiliser les <code>Chunk</code> correspondant aux tarifs exclus. C'est donc le but de notre méthode : <strong><em>compter le nombre de <code>Chunk</code> a ne pas comptabiliser lors de la tarification.</em></strong>

Voici le code de test extrait de la méthode originale. Pour aider à comprendre, il faut passer un peu de temps sur la méthode <code>prepareTavel()</code> qui crée un <code>Travel</code> avec ses morceaux, ses tarifs, ses passagers :

[java]
@InjectMocks
private ChunksFromTravelBillingExcluder sut; // System Under Test
@Mock
private Billing billing;

/**
 * nom de méthode de test pourri
 * mais je ne comprends pas encore exactement comment ca fonctionne
 */
@Test
public void should_compute() {
    // GIVEN
    final Travel travel = prepareTravel();

    // WHEN
    when(billing.getExcludedFares()).thenReturn(
            new FareLabel[]{
                 FareLabel.of(&quot;EXCLUDED_FARE&quot;), 
                 FareLabel.of(&quot;EXCLUDED_FARE_2&quot;)
            });

    final int actualQuantity = sut.count(travel);

    // THEN
    assertEquals(18, actualQuantity);
}


private Travel prepareTravel() {

    final FarePolicy farePolicy1 = new FarePolicy();
    farePolicy1.setCustomerType(CustomerType.of(&quot;CUSTOMER_TYPE_1&quot;));
    farePolicy1.setChunks(new Chunk[]{new Chunk(), new Chunk()});
    final FareLabel fare1 = FareLabel.of(&quot;FARE_1&quot;);
    farePolicy1.setFareLabel(fare1);

    final FarePolicy farePolicy2 = new FarePolicy();
    farePolicy2.setCustomerType(CustomerType.of(&quot;CUSTOMER_TYPE_2&quot;));
    farePolicy2.setChunks(new Chunk[]{new Chunk(), new Chunk(), new Chunk()});
    farePolicy2.setFareLabel(FareLabel.of(&quot;EXCLUDED_FARE&quot;));

    final CustomerFare customerFare1 = new CustomerFare();
    customerFare1.setCustomerType(CustomerType.of(&quot;CUSTOMER_TYPE_1&quot;));
    customerFare1.setCustomersQuantity(2);

    final CustomerFare customerFare2 = new CustomerFare();
    customerFare2.setCustomerType(CustomerType.of(&quot;CUSTOMER_TYPE_2&quot;));
    customerFare2.setCustomersQuantity(3);

    final Price price = new Price();
    price.setFarePolicies(new FarePolicy[]{farePolicy1, farePolicy2});
    price.setCustomerFares(new CustomerFare[]{customerFare1, customerFare2});

    final Travel travel = new Travel();
    travel.setChunks(new Chunk[]{new Chunk(), new Chunk(), new Chunk(), new Chunk()});

    final CustomersGroup customersGroup1 = new CustomersGroup();
    customersGroup1.setCustomers(new Customer[]{
                                     new Customer(), new Customer(), new Customer(),
                                     new Customer(), new Customer()});
    travel.setCustomersGroups(new CustomersGroup[]{customersGroup1});
    travel.setPrices(new Price[]{price, price});
    return travel;
}
[/java]

Je sais d'après la méthode de test que si l'on passe ce <code>Travel</code> dans notre méthode <code>compute(...)</code>, on doit obtenir <code>18</code>.
En effet pour le tarif à exclure <code>EXCLUDED_FARE</code>, c'est la <code>farePolicy2</code> qui comporte 3 <code>Chunk</code> et le <code>CUSTOMER_TYPE_2</code> qui est concernée. Et la <code>customerFare2</code> concerne 3 passagers de type <code>CUSTOMER_TYPE_2</code>.
Les deux <code>Price</code> étant identiques, on a

<blockquote>
  3 <code>Chunk</code> * 3 passagers + 3 <code>Chunk</code> * 3 passagers = <strong>18</strong>
</blockquote>

C'est en fait ce code de test qui m'a permis de comprendre l'implémentation (une preuve supplémentaire de pourquoi il faut tester et proprement de surcroît ;) )
Même si le test est un peu complexe à comprendre il couvre une large palettes de cas.
On pourrait découper les cas de tests mais ce n'est pas l'objet de cet article.

<h3>Récapitulons</h3>

<ul>
<li>pour chaque <code>Price</code>

<ul>
<li>on liste les <code>CustomerType</code> concernés par les exclusions de tarif</li>
<li>pour chaque <code>CustomerType</code> à exclure

<ul>
<li>on cherche le <code>FarePolicy</code> correspondant à exclure</li>
<li>on cherche le <code>CustomerFare</code> correspondant à exclure</li>
<li>on combine le <code>FarePolicy</code> et le <code>CustomerFare</code> obtenus pour obtenir le nombre de <code>Chunk</code> à exclure</li>
</ul></li>
<li>on additionne les nombres obtenus</li>
</ul></li>
<li>on additionne le nombre obtenu pour chaque <code>Price</code></li>
</ul>

<h2>Enfin un peu de Java 8</h2>

Convertissons tout ça en Java 8.
Premièrement, ce qui saute aux yeux : on doit avoir une fonction qui prend un <code>Price</code> qui retourne un <code>int</code>. Ce qui correspond à notre méthode <code>countExclusionsForPrice</code> précédente qu'on pourra référencer directement dans nos <code>Stream</code> via l'opérateur <code>::</code> : <code>this::countExclusionsForPrice</code>
On verra son implémentation par la suite.

On aura une fonction qui combine le <code>FarePolicy</code> et le <code>CustomerFare</code> et qui retourne un <code>int</code>, une <code>ToIntBiFunction&lt;FarePolicy, CustomerFare&gt;</code>. On appliquera cette lambda à chaque couple <code>FarePolicy</code>, <code>CustomerFare</code> correspondant à une exclusion. Je ne sais pas encore <a href="http://docs.oracle.com/javase/8/docs/api/java/util/function/class-use/ToIntBiFunction.html">quand et comment</a> on pourra l'appliquer.
[java]
public int countExclusionsForFare(FarePolicy policy, CustomerFare faure) {
    return farePolicy.getChunks().length * customerFare.getCustomersQuantity();
}
[/java]
Pour lister les <code>CustomerType</code> concernés par les exclusions de tarif pour un <code>Price</code>, là c'est facile : on liste l'ensemble des <code>FarePolicy</code> d'un <code>Price</code>, on ne garde que celles contenues dont le <code>TarifLabel</code> est contenu dans les tarifs exclus. Puis on récupère le <code>CustomerType</code> de chaque <code>FarePolicy</code>.
[java]
private Stream&lt;CustomerType&gt; findExcludedCustomerTypes(Price price) {
    return Stream.of(price.getFarePolicies())
            .filter(fp -&gt; excludedFares().contains(fp.getFareName()))
            .map(FarePolicy::getCustomerType);
}
...
private List&lt;FareLabel&gt; excludedFares() {
    // retourne la liste des tarifs exclus
}
[/java]

Essayons maintenant d'implémenter notre  <code>countExclusionsForPrice(Price)</code> qui compte le nombre de <code>Chunk</code> à exclure pour un <code>Price</code>. Maintenant que j'ai récupéré les tarifs à exclure pour chaque <code>Price</code>, cherchons les combinaisons de <code>FarePolicy</code> et <code>CustomerFare</code> à partir de ces infos. 
Celà signifie qu'à partir d'un <code>Price</code> et d'un <code>CustomerType</code> à exclure je dois :
1.retrouver le <code>FarePolicy</code> correspondant
2.retrouver le <code>CustomerFare</code> correspondant
3.enfin calculer le nombre de <code>Chunk</code> à exclure.

NB : En théorie, je devrais toujours avoir une correspondance 1-1 entre mes <code>FarePolicy</code> et mes <code>CustomerFare</code> et mes exclusions. N'en étant pas absolument certain, je vais utiliser des <code>Optional&lt;&gt;</code> pour accepter la correspondance 1-0. Il est par contre certain que je n'aurais pas de correspondance 1-n, je ne vais donc pas traiter le cas des listes.

Cela donne 
[java]
public Optional&lt;FarePolicy&gt; findExcludedFarePolicy(final Price price, final CustomerType excluded) {
    return findExcludedItem(price, 
                            Price::getFarePolicies, 
                            c -&gt; c.getCustomerType().equals(excluded));
}

public Optional&lt;CustomerFare&gt; findExcludedCustomerFare(final Price price, final CustomerType excluded) {
    return findExcludedItem(price, 
                            Price::getCustomerFares, 
                            c -&gt; c.getType().equals(excluded));
}

private &lt;I&gt; Optional&lt;I&gt; findExcludedItem(Price price,
                                         Function&lt;Price, I[]&gt; itemsExtractor,
                                         Predicate&lt;I&gt; itemsFinder) {
    return Stream.of(itemsExtractor.apply(price))
            .filter(itemsFinder)
            .findFirst();
}
[/java]

En ce qui concerne la <code>countExclusionsForFare(FarePolicy,CustomerFare)</code> et avec les <code>Optional&lt;&gt;</code> on aura ça
[java]
private int countExclusionsForFare(Optional&lt;FarePolicy&gt; farePolicy, Optional&lt;CustomerFare&gt; customerFare) {
    return farePolicy.isPresent() &amp;&amp; customerFare.isPresent() ?
            farePolicy.get().getChunks().length 
                * customerFare.get().getCustomersQuantity() :
            DEFAULT;
}
[/java]
En combinant tout ça, je peux calculer un nombre d'exclusions pour un <code>Price</code> et un <code>CustomerType</code> exclu
[java]
private int countExclusionsForPriceByCustomerType(Price price, CustomerType customerType) {
    final Optional&lt;FarePolicy&gt; farePolicy = findExcludedFarePolicy(price, customerType);
    final Optional&lt;CustomerFare&gt; customerFare = findExcludedCustomerFare(price,
                                                                         customerType);
    return this.countExclusionsForFare(farePolicy, customerFare);
}
[/java]

En utilisant l'application partielle ou <a href="https://fr.wikipedia.org/wiki/Curryfication">curryfication</a> du <code>Price</code>, j'obtiens très simplement l'implémentation de ma fonction<code>countExclusionsForPrice(Price)</code> que je pourrais appliquer très simplement à un <code>Stream</code> des <code>Price</code> d'un <code>Travel</code>
[java]
private int countExclusionsForPrice(final Price price) {
    final Stream&lt;CustomerType&gt; excludedCustomerTypes = findExcludedCustomerTypes(price);
    return excludedCustomerTypes
            .mapToInt(
                 customerType -&gt; this.countExclusionsForPriceByCustomerType(price,        
                                                                            customerType))
            .sum();
}
[/java]
ce que l'on peut aussi écrire comme ça 
[java]
private int countExclusionsForPrice(final Price price) {
    return Optional.of(price)
            .map(this::findExcludedCustomerTypes)
            .orElse(Stream.empty())
            .mapToInt(
                 customerType -&gt; this.countExclusionsForPriceByCustomerType(price,
                                                                            customerType))
            .sum();
}
[/java]

Ainsi pour obtenir le résultat, j'utilise le code suivant 
[java]
public int count(final Travel travel) {
    if (!acceptPreconditions(travel)) {
        return DEFAULT;
    }
    return countChunksExclusions(travel.getPrices());
}

private int countChunksExclusions(Price... prices) {
    return Stream.of(prices)
            .mapToInt(this::countExclusionsForPrice)
            .sum();
}
[/java]
Ou encore si on est joueur et en utilisant notre <code>acceptPreconditions()</code> juste pour tester le <code>billing</code>
[java]
private boolean acceptPreconditions() {
    return billing != null &amp;&amp; !ArrayUtils.isEmpty(billing.getExcludedFares());
}

public int count(final Travel travel) {
    if(!acceptPreconditions()) {
        return DEFAULT;
    }
    return Optional.ofNullable(travel.getPrices())
            .map(Stream::of)
            .orElse(Stream.empty())
            .mapToInt(this::countExclusionsForPrice)
            .sum();
}
[/java]

<h1>Conclusion</h1>

A première vue, le résultat n'est pas incroyable. En effet, il y a beaucoup de petites méthodes privées.

<h3>Les aspects négatifs</h3>

<ul>
<li>a première vue, c'est confus, il y a beaucoup de méthodes à peu de lignes</li>
<li>le code semble un peu explosé </li>
<li>il y a plus de lignes de codes</li>
</ul>

<h3>Le positif</h3>

<ul>
<li>au delà de l'aspect visuel (un simple méthode de 10 lignes, c'est plus beau que 10 méthodes de 1 ligne), la rentrée dans le code est plus simple</li>
<li>les concepts sont mieux définis et peuvent se composer, s'enchaîner (ce qui coûte en nombre de lignes de code)</li>
<li>le code est totalement composable, il est donc plus évolutif et de plus sans effet de bords</li>
</ul>

<h3>Ce que j'en retire</h3>

Malgré de nombreuses explications et lectures sur le sujet, grâce à cet exercice j'ai enfin compris/acquis en profondeur les concepts d'application partielle ou curryfication (c'est à dire que je me souviendrais ce que ça veut dire ;)) .
Autre chose, une fois les concepts à peu près maîtrisés, il est difficile de savoir quand s'arrêter d'écrire en fonctionnel. En effet, les notations Java 8 étant récentes, lire le code produit est parfois déroutant. Il faut donc savoir écrire du code à la hauteur de la maîtrise de l'équipe pour ne pas retrouver notre code à nouveau réécrit. 
Par exemple, il n'est pas évident pour un néophyte de comprendre et penser qu'une méthode avec la signature suivante 
<code>int conversionEnInt(MonObjet)</code> peut s'utiliser comme une <code>ToIntFunction&lt;MonObjet&gt;</code> lors de la manipulation d'un <code>Stream&lt;MonObjet&gt;</code>. J'en profite pour remercier <a href="https://twitter.com/aloyer">Arnauld Loyer</a> qui m'a fait remarquer que cet aspect des choses qui s'appliquait aux classes du JDK s'appliquait aussi aux miennes. Ce qui m'a permis de gagner en lisibilité.

À propos de clean code et de la lisibilité du Java 8, j'ai eu le plaisir d'assister à une présentation de Rémi Forax sur les <a href="https://speakerdeck.com/forax/design-pattern-reloaded-parisjug">Design Pattern reloaded en Java 8</a>. Sa présentation bien que très intéressante finissait de manière très ardue. Lorsque je lui ai demandé si ce qu'il avait présenté était du clean code, il m'a répondu "c'est du clean code de demain".

J'ai beaucoup aimé cette réponse qui signifie pour moi que c'est normal si notre avis sur nos productions Java 8 évolue au cours du temps. Mais surtout, il faut essayer quitte à en faire un peu trop au début. En effet, c'est en poussant le bouchon et en ayant pas peur de déconstruire les raisonnements initiaux que j'ai fini par obtenir une version qui me semble raisonnable (même si elle peut être encore futuriste pour certains coéquipiers). Et oui j'ai présenté cet article comme si mon raisonnement avait été linéaire, ce qui est loin d'être le cas.

J'en profite pour remercier <a href="https://twitter.com/mrcafetux">M. Cafetux</a> pour sa relecture qui en m'ayant poussé dans mes retranchements m'a permis d'obtenir une version bien plus intéressante d'un point de vue fonctionnel.

Pour conclure, selon moi, grâce aux interfaces fonctionnelles, on a maintenant un code beaucoup plus expressif qu'avant. Au delà de quelques lourdeurs  (ex : <code>.stream()</code>) liées à la rétrocompatibilité et ces nouvelles notations qu'il faut acquérir, je trouve que le code ainsi découpé est bien plus facilement lisible. On peut ensuite se plonger ou non dans les parties qui nous posent question.

Vous trouverez ci-dessous la version refactorée de ma classe
[java]
private Billing billing;
private final static int DEFAULT = 0;

public ChunksFromTravelBillingExcluder(final Billing billing) {
    this.billing = billing;
}

private boolean acceptPreconditions() {
    return billing != null &amp;&amp; !ArrayUtils.isEmpty(billing.getExcludedFares());
}

public int count(final Travel travel) {
    if(!acceptPreconditions()) {
        return DEFAULT;
    }
    return Optional.ofNullable(travel.getPrices())
            .map(Stream::of)
            .orElse(Stream.empty())
            .mapToInt(this::countExclusionsForPrice)
            .sum();
}

private int countExclusionsForPrice(final Price price) {
    return Optional.of(price)
            .map(this::findExcludedCustomerTypes)
            .orElse(Stream.empty())
            .mapToInt(
                 customerType -&gt; this.countExclusionsForPriceByCustomerType(price,
                                                                            customerType))
            .sum();
}

private int countExclusionsForPriceByCustomerType(Price price, CustomerType customerType) {
    final Optional&lt;FarePolicy&gt; farePolicy = findExcludedFarePolicy(price, customerType);
    final Optional&lt;CustomerFare&gt; customerFare = findExcludedCustomerFare(price, 
                                                                         customerType);
    return this.countExclusionsForFare(farePolicy, customerFare);
}

private int countExclusionsForFare(Optional&lt;FarePolicy&gt; farePolicy, Optional&lt;CustomerFare&gt; customerFare) {
    return farePolicy.isPresent() &amp;&amp; customerFare.isPresent() ?
            farePolicy.get().getChunks().length 
                * customerFare.get().getCustomersQuantity() :
            DEFAULT;
}

private Optional&lt;FarePolicy&gt; findExcludedFarePolicy(final Price price, final CustomerType excluded) {
    return findExcludedItem(price, 
                            Price::getFarePolicies, 
                            c -&gt; c.getCustomerType().equals(excluded));
}

private Optional&lt;CustomerFare&gt; findExcludedCustomerFare(final Price price, final CustomerType excluded) {
    return findExcludedItem(price, 
                            Price::getCustomerFares, 
                            c -&gt; c.getType().equals(excluded));
}

private &lt;I&gt; Optional&lt;I&gt; findExcludedItem(Price price,
                                         Function&lt;Price, I[]&gt; itemsExtractor,
                                         Predicate&lt;I&gt; itemsFinder) {
    return Stream.of(itemsExtractor.apply(price))
            .filter(itemsFinder)
            .findFirst();
}

private Stream&lt;CustomerType&gt; findExcludedCustomerTypes(Price price) {
    return Stream.of(price.getFarePolicies())
            .filter(fp -&gt; excludedFares().contains(fp.getFareName()))
            .map(FarePolicy::getCustomerType);
}

private List&lt;FareLabel&gt; excludedFares() {
    return Arrays.asList(billing.getExcludedFares());
}
[/java]
