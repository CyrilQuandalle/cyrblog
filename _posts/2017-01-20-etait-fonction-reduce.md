---
layout: post
title: Il était une fois la fonction reduce
date: 2017-01-20 11:41
author: patrick giry
comments: true
categories: [Bonnes pratiques de dév, Fonctionnel, fonctionnel, java, java8, Programmation, programming]
---
Imaginons une collection d'entiers:

[code language="java"]
    Collection&lt;Integer&gt; integers = Arrays.asList(1, 2, 3, 4)
[/code]

Dans une approche impérative, lorsque nous voulons calculer une valeur à partir d'une liste de valeurs, nous devons utiliser une boucle sur la liste et accumuler le résultat à l'extérieur de la boucle. Par exemple, pour calculer la somme des entiers, nous procédons comme suit :

[code language="java"]
    int sum = 0; 
    for (int i : integers) { 
      sum += i; 
    } 
[/code]

Dans une approche fonctionnelle, nous déléguons ce traitement à une fonction nommée <code>reduce</code>. Pour sommer les entiers, nous procédons comme ceci :

[code language="java"]
    reduce((sum, i) -&gt; sum + i, 0, integers)
[/code]

Le traitement à effectuer est passé en paramètre en définissant une <i> fonction de réduction </i> (<code>(sum, i) -&gt; sum + i</code>).

Savez-vous qu'avec cette <i> fonction de réduction </i> on peut définir les fonctions <code>map</code>et <code>filter</code>? On peut même combiner les <code>map</code>et <code>filter</code> pour former une <i> fonction de réduction </i> qui s'applique à une collection sans avoir une d’intermédiaire. Cette technique s'appelle les <a href="http://clojure.org/reference/transducers" target="_blank"> Transducers</a>. Les <a href="http://clojure.org/reference/transducers" target="_blank"> Transducers</a> sont un concept qui vient de l'univers Clojure. Mais, même en Clojure, tout le monde ne les connait pas !
En ce qui me concerne, je les ai découverts grâce aux excellentes <a href="https://speakerdeck.com/lilobase/des-boucles-aux-transducers-meetup-craftingsw-paris-2016">présentations</a> d'Arnaud Lemaire (<a href="https://twitter.com/Lilobase">@lilobase</a>). Il utilise des langages dynamiques non typés (python, PHP, etc...) pour sa démonstration. 
J'ai donc décidé de relever le défi : coder les <a href="http://clojure.org/reference/transducers" target="_blank"> Transducers</a> en Java 8 avec ses types génériques qui ne sont pas toujours simples à dompter. Comme si cela ne suffisait pas, je vais en profiter pour montrer comment cela fonctionne ! Ainsi, nous verrons que ce concept devient inévitable pour tout craftman qui se respecte refactorant régulièrement son code. N’est-ce pas ?

A l’aide d’un exemple concret (calculer le montant total des factures dues pour un mois donné) nous partirons d’une implémentation impérative et nous nous dirigerons vers une implémentation fonctionnelle. Au passage, nous verrons comment écrire les fonctions <code>reduce</code>, <code>map</code> et <code>filter</code>. 
Ainsi, en refactorant successivement notre fonction de calcul nous aboutirons au développement et à l'utilisation des <a href="http://clojure.org/reference/transducers" target="_blank"> Transducers</a>.

<h2>Une collection de factures</h2>

Pour faire cette démonstration, nous utiliserons les factures suivantes :

<table><caption>Liste des factures</caption>
<tbody>
<tr>
<th>Due pour le mois</th>
<th>Montant H.T.</th>
<th>TVA</th>
<th>Quantité</th>
<th>Payée</th>
</tr>
<tr>
<td>Octobre</td>
<td>36.0</td>
<td>20 %</td>
<td>1</td>
<td>Non</td>
</tr>
<tr>
<td>Septembre</td>
<td>28.0</td>
<td>10 %</td>
<td>1</td>
<td>Oui</td>
</tr>
<tr>
<td>Octobre</td>
<td>17.0</td>
<td>5 %</td>
<td>2</td>
<td>Non</td>
</tr>
<tr>
<td>Octobre</td>
<td>27.0</td>
<td>5 %</td>
<td>2</td>
<td>Oui</td>
</tr>
</tbody>
</table>

En Java, pour représenter le tableau ci-dessus, nous construisons la collection de factures (<code>Invoice</code>) comme suit:

[code language="java"]    
    static Collection&lt;Invoice&gt; invoices() {
        return Arrays.asList( //
                anInvoice(invoiceBuilder -&gt; {
                    invoiceBuilder.dueTo(Month.OCTOBER);
                    invoiceBuilder.amountExcludeVAT(36.0);
                    invoiceBuilder.vatRate(20.0);
                    invoiceBuilder.quantity(1);
                    invoiceBuilder.mustBePaid();
                }), //
                anInvoice(invoiceBuilder -&gt; {
                    invoiceBuilder.dueTo(Month.SEPTEMBER);
                    invoiceBuilder.amountExcludeVAT(28.0);
                    invoiceBuilder.vatRate(10.0);
                    invoiceBuilder.quantity(1);
                    invoiceBuilder.isPaid();
                }), //
                anInvoice(invoiceBuilder -&gt; {
                    invoiceBuilder.dueTo(Month.OCTOBER);
                    invoiceBuilder.amountExcludeVAT(17.0);
                    invoiceBuilder.vatRate(5.0);
                    invoiceBuilder.quantity(2);
                    invoiceBuilder.mustBePaid();
                }), //
                anInvoice(invoiceBuilder -&gt; {
                    invoiceBuilder.dueTo(Month.OCTOBER);
                    invoiceBuilder.amountExcludeVAT(27.0);
                    invoiceBuilder.vatRate(5.0);
                    invoiceBuilder.quantity(2);
                    invoiceBuilder.isPaid();
                }));
    }
[/code]

Les factures sont définies comme suit:

[code language="java"]
    public class Invoice {
        private final Month dueTo;
        private final Amount amountExcludeVAT;
        private final Quantity quantity;
        private final Rate vatRate;
        private final boolean paid;

        public static Invoice anInvoice(Consumer&lt;Builder&gt; consumer) {
            Builder builder = new Builder();
            consumer.accept(builder);
            return builder.build();
        }

        private Invoice(Month dueTo, Amount amountExcludeVAT, Quantity quantity, 
                        Rate vatRate, boolean paid) {
            this.dueTo = dueTo;
            this.amountExcludeVAT = amountExcludeVAT;
            this.quantity = quantity;
            this.vatRate = vatRate;
            this.paid = paid;
        }

        public boolean isDueFor(Month month) { return !paid &amp;&amp; dueTo == month; }

        public Amount totalIncludingVAT() { 
            return amountExcludeVAT.add(VATAmount()).multiply(quantity); 
        }
 
        private Amount VATAmount() { 
             return amountExcludeVAT.divideByHundred().multiply(vatRate); 
        }

        public static class Builder {
            private Month dueMonth;
            private Amount amountExcludeVAT;
            private Quantity quantity;
            private Rate vatRate;
            private boolean paid;

            private Builder() {}

            public void dueTo(Month month) { dueMonth = month; }

            public void amountExcludeVAT(double amountExcludeVAT) { 
                this.amountExcludeVAT = Amount.of(amountExcludeVAT); 
            }

            public void quantity(long quantity) { this.quantity = Quantity.of(quantity); }

            public void vatRate(double vatRate) { this.vatRate = Rate.of(vatRate); }

            public void mustBePaid() { paid = false; }

            public void isPaid() { paid = true; }

            private Invoice build() {
                return new Invoice(dueMonth, amountExcludeVAT, quantity, vatRate, paid);
            }
        }
    }

    public class Amount {
        public static final Amount HUNDRED = Amount.of(100);
        public static final Amount ZERO = Amount.of(BigDecimal.ZERO);
    
        private final BigDecimal value;

        public static Amount of(double amountAsDouble) {
            return Amount.of(BigDecimal.valueOf(amountAsDouble));
        }
    
        public static Amount of(BigDecimal amountAsBigDecimal) { 
            return new Amount(Objects.requireNonNull(amountAsBigDecimal)); 
        }

        private Amount(BigDecimal value) {
            this.value = value.setScale(2, BigDecimal.ROUND_DOWN);
        }

        public Amount add(Amount otherAmount) {
            return new Amount(value.add(otherAmount.value));
        }

        public Amount multiply(Quantity quantity) {
            return Amount.of(value.multiply(quantity.asBigDecimal()));
        }

        public Amount divideByHundred() {
            return Amount.of(value.divide(Amount.HUNDRED.value, 2, BigDecimal.ROUND_DOWN));
        }

        public Amount multiply(Rate vatRate) {
            return Amount.of(value.multiply(vatRate.asBigDecimal()));
        }
    }

    class Quantity  {
        private final long value;

        static Quantity of(long value) { return new Quantity(value); }

        private Quantity(long value) { this.value = value; }

        BigDecimal asBigDecimal() { return BigDecimal.valueOf(value); }

    }

    class Rate {
        private final double value;

        static Rate of(double value) { return new Rate(value); }

        private Rate(double value) { this.value = value; }

        BigDecimal asBigDecimal() { return BigDecimal.valueOf(value); }

    }
[/code]

Nous allons calculer le montant total des factures dues pour le mois d'octobre. On commence donc par un test :

[code language="java"]
    assertThat(getAmountOf(Month.OCTOBER)).isEqualTo(Amount.of(78.90));
[/code]

<h2>Première implémentation en style impératif</h2>

[code language="java"]
    Amount getAmountOf(Month month) {
        Amount totalAmount = Amount.ZERO;

        for (Invoice invoice: invoices()) {
            if (invoice.isDueFor(month)) {
                totalAmount = totalAmount.add(invoice.totalIncludingVAT());
            }
        }
        return totalAmount;
    }
[/code]

Cette implémentation se fait avec les étapes suivantes :

<ul>
    <li>Nous définissons une variable `totalAmount` et nous l'initialisons avec la valeur initiale zéro.</li>
    <li>Puis nous bouclons sur la liste des factures.</li>
    <li>Pour chaque facture nous vérifions si elle est due pour le mois donné.</li>
    <li>Si la facture est due:
<ul>
    <li>Nous calculons son montant toutes taxes comprises;</li>
    <li>nous ajoutons son montant toutes taxes comprises au total des factures dues.</li>
</ul>
</li>
</ul>

<h2>Deuxième implémentation avec un <code>filter</code>, un <code>map</code> et un <code>reduce</code></h2>

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = 
            filter(invoice -&gt; invoice.isDueFor(month), invoices());
        List&lt;Amount&gt; includeVATAmounts = 
            map(Invoice::totalIncludingVAT, invoicesOfTheMonth);
        return reduce(Amount::add, Amount.ZERO, includeVATAmounts);
    }
[/code]

Avec cette implémentation, nous avons les étapes suivantes :

<ul>
<li>Sélectionner toutes les factures dues dans le mois spécifié (<code>filter</code>).</li>
<li>Transformer la liste des factures dues en une liste de montants toutes taxes comprises (<code>map</code>).</li>
<li>Calculer la somme des montants dus pour la liste de montants toutes taxes comprises (<code>reduce</code>).</li>
</ul>

Dans cette implémentation nous voyons que:

<ul>
<li>Chaque étape à réaliser correspond à une déclaration (on définit ce que c'est et non comment on le fait).</li>
<li>Les paramètres en entrée ne sont pas modifiés.</li>
<li>Les étapes et comportements sont isolés.</li>
<li>Les effets de bords sont supprimés.</li>
<li>Les comportements peuvent être réutilisés.</li>
<li>Le code est plus modulaire.</li>
</ul>

Cette implémentation pourrait être réalisée avec une version de Java inférieure à 8, en utilisant les classes anonymes :

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = filter(new Predicate&lt;Invoice&gt;() {
            @Override
            public boolean test(Invoice invoice) {
                return invoice.isDueFor(month);
            }
        }, invoices());
        List&lt;Amount&gt; includeVATAmounts = map(new Function&lt;Invoice, Amount&gt;() {
            @Override
            public Amount apply(Invoice invoice) {
                return invoice.totalIncludingVAT();
            }
        }, invoicesOfTheMonth);
        return reduce(new BiFunction&lt;Amount, Amount, Amount&gt;() {
            @Override
            public Amount apply(Amount amount, Amount otherAmount) {
                return amount.add(otherAmount);
            }
        }, Amount.ZERO, includeVATAmounts);
    }
[/code]

Il faut aussi définir les interfaces suivantes:

<h3>Un prédicat</h3>

[code language="java"]
    public interface Predicate&lt;T&gt; {
        boolean test(T t);
    }
[/code]

<h3>Une fonction avec un seul paramètre</h3>

[code language="java"]
    public interface Function&lt;T, R&gt; {
        R apply(T t);
    }
[/code]

<h3>Une fonction avec deux paramètres</h3>

[code language="java"]
    public interface BiFunction&lt;T,U,R&gt; {
        R apply(T t, U u);
    }
[/code]

L'écriture est moins concise qu'avec les <code>lambdas</code> et les <code>méthodes références</code>.

Pour exécuter cela et montrer comme ça marche nous allons implémenter de façon impérative les méthodes <code>reduce</code>, <code>map</code> et <code>filter</code>.

<h3><code>reduce</code></h3>

[code language="java"]
    static &lt;T, R&gt; R reduce(BiFunction&lt;R, T, R&gt; reducer, 
                           R identity, Collection&lt;T&gt; collection) {
        R accumulator = identity;
        for (T element: collection) {
            accumulator = reducer.apply(accumulator, element);
        }
        return accumulator;
    }
[/code]

La fonction <code>reduce</code> prend en paramètre:

<ul>
<li>La <i> fonction de réduction </i> (<code>reducer</code>);</li>
<li>L'élément d'identité ou l'élément neutre (<code>identity</code>);</li>
<li>Les éléments à réduire (<code>collection</code>).</li>
</ul>

Elle retourne le résultat après avoir appliqué la <i> fonction de réduction </i> autant de fois qu'il y a d'éléments à réduire.

<h3>La fonction de réduction</h3>

[code language="java"]
    R reducer(R accumulator, T element)
[/code]

C'est une fonction qui prend deux paramètres:

<ul>
<li>Le résultat accumulé (<code>accumulator</code>);</li>
<li>L'entrée à traiter (<code>element</code>).</li>
</ul>

Elle retourne le résultat accumulé (<code>result</code>) après traitement.

En java 8 nous pouvons écrire <code>reduce</code> de manière plus fonctionnelle avec l'API <code>Stream</code>:

[code language="java"]
    static &lt;T, R&gt; R reduce(BiFunction&lt;R, T, R&gt; reducer, 
                           R identity, 
                           BinaryOperator&lt;R&gt; combiner, 
                           Collection&lt;T&gt; collection) {
        return collection.stream()
                         .reduce(identity, reducer, combiner);
    }
[/code]

Il faut alors un paramètre supplémentaire requis (<code>combiner</code>) qui est utilisé pour reconstituer le résultat final si la fonction <code>reduce</code> s'est exécutée sur des <code>Stream</code> parallèles.

[code language="java"]
    static &lt;T, R&gt; R parallelReduce(BiFunction&lt;R, T, R&gt; reducer, 
                                   R identity, 
                                   BinaryOperator&lt;R&gt; combiner,
                                   Collection&lt;T&gt; collection) {
        return collection.parallelStream()
                         .reduce(identity, reducer, combiner);
    }
[/code]

<h3><code>map</code></h3>

[code language="java"]
    static &lt;T, R&gt; List&lt;R&gt; map(Function&lt;T, R&gt; mapper, 
                              Collection&lt;T&gt; collection) {
        List&lt;R&gt; accumulator = new ArrayList&lt;&gt;();
        for (T element: collection) {
            accumulator.add(mapper.apply(element));
        }
        return accumulator;
    }
[/code]

La fonction <code>map</code> prend en paramètre :

<ul>
<li>Une fonction pour transformer tous les éléments fournis en entrée (<code>mapper</code>)</li>
<li>L'ensemble des éléments à transformer (<code>collection</code>).</li>
</ul>

Elle retourne la liste des éléments transformés.

<h3>La fonction de transformation</h3>

[code language="java"]
    R mapper(T element) 
[/code]

C'est une fonction qui prend en paramètre l'élément à transformer.
Elle retourne l'élément transformé.

En java 8 nous pouvons écrire <code>map</code> de manière plus fonctionnelle avec l'API <code>Stream</code>:

[code language="java"]
    static &lt;T, R&gt; List&lt;R&gt; map(Function&lt;T, R&gt; mapper, 
                              Collection&lt;T&gt; collection) {
        return collection.stream()
                         .map(mapper).collect(Collectors.toList());
    }
[/code]

<h3><code>filter</code></h3>

[code language="java"]
    static  &lt;T&gt; List&lt;T&gt; filter(Predicate&lt;T&gt; predicate, 
                               Collection&lt;T&gt; collection) {
        List&lt;T&gt; accumulator = new ArrayList&lt;&gt;();
        for(T element: collection) {
            if (predicate.test(element)) {
                accumulator.add(element);
            }
        }
        return accumulator;
    }
[/code]

La fonction <code>filter</code> prend en paramètre:

<ul>
<li>Un prédicat qui est utilisé pour sélectionner les éléments (<code>predicate</code>).</li>
<li>L'ensemble des éléments à sélectionner en fonction du prédicat (<code>collection</code>).</li>
</ul>

Elle retourne tous les éléments sélectionnés (ceux pour lesquels le prédicat est vrai).

<h3>Le prédicat</h3>

[code language="java"]
    boolean predicate(T element)
[/code]

C'est une fonction qui prend l'élément à tester en paramètre.
Elle retourne <code>true</code> si le prédicat est vrai pour l'élément, <code>false</code> sinon.

En java 8 nous pouvons écrire <code>filter</code> de manière plus fonctionnelle avec l'API <code>Stream</code>:

[code language="java"]
    static &lt;T&gt; List&lt;T&gt; filter(Predicate&lt;T&gt; predicate, 
                              Collection&lt;T&gt; collection) {
        return collection.stream()
                         .filter(predicate)
                         .collect(Collectors.toList());
    }
[/code]

<h3><code>reduce</code>peut accumuler des non-scalaires</h3>

Supposons que nous voulions compter le nombre d'occurence de chaque lettre dans la phrase <i> "Il était une fois une fonction reduce" </i>. Nous devrions obtenir le résultat suivant:

[code language="java"]    
     Map&lt;Character, Long&gt; expectedResult = new HashMap&lt;&gt;();
        expectedResult.put(' ', 6L);
        expectedResult.put('I', 1L);
        expectedResult.put('a', 1L);
        expectedResult.put('c', 2L);
        expectedResult.put('d', 1L);
        expectedResult.put('e', 4L);
        expectedResult.put('f', 2L);
        expectedResult.put('i', 3L);
        expectedResult.put('l', 1L);
        expectedResult.put('n', 4L);
        expectedResult.put('o', 3L);
        expectedResult.put('r', 1L);
        expectedResult.put('s', 1L);
        expectedResult.put('t', 3L);
        expectedResult.put('u', 3L);
        expectedResult.put('é', 1L);

        assertThat(result).isEqualTo(expectedResult);
[/code]

Pour construire le résultat attendu, ici une <code>Map</code>, nous pouvons utiliser <code>reduce</code>:

[code language="java"]
    List&lt;Character&gt; listOfCharacters = 
         listOfCharacters(&quot;Il était une fois une fonction reduce&quot;);
    
    Map&lt;Character, Long&gt; result = reduce(accLetter, 
                                         new HashMap&lt;&gt;(), 
                                         listOfCharacters);
[/code]

Nous transformons la phrase en liste de caractères comme suit:

[code language="java"]    
    private List&lt;Character&gt; listOfCharacters(String s) {
        return s.chars()
                .mapToObj(c -&gt; (char) c).collect(toList());
    }
[/code]

La <i> fonction de réduction </i> (<code>accLetter</code>) est la suivante:

[code language="java"]        
    final BiFunction&lt;Map&lt;Character, Long&gt;, Character,
                     Map&lt;Character, Long&gt;&gt; accLetter =
        (accumulator, letter) -&gt; {
            accumulator.put(letter,
                       accumulator.getOrDefault(letter, 0L) + 1L);
            return accumulator;
        };
[/code]

Elle prend en paramètres (<code>accumulator</code>) la <code>Map</code> contenant les compteurs de lettres dejà existants et la nouvelle lettre (<code>letter</code>) à traiter. Elle retourne la <code>Map</code> contenant les compteurs de lettres mis à jour.

[code language="java"]
    Map&lt;Character, Long&gt; accLetter(
          Map&lt;Character, Long&gt; letterCounters, Character letter) 
[/code]

<h2>Troisième implémentation les <code>transducers</code></h2>

Cette deuxième implémentation a les défauts suivants:

<ul>
<li>L'ensemble des éléments est parcouru à chaque étape de transformation;</li>
<li>Chaque étape de transformation crée une collection intermédiaire;</li>
<li>Chaque étape supplémentaire augmente le temps de calcul et la mémoire consommée.</li>
</ul>

Dans la troisième implémentation nous allons procéder par étapes pour arriver au final aux <code>Transducers</code>

<h3>Première étape: exprimons <code>map</code>et <code>filter</code> via <code>reduce</code></h3>

[code language="java"]
    static &lt;T, R&gt; R reduce(BiFunction&lt;R, T, R&gt; reducer, 
                           R identity, 
                           BinaryOperator&lt;R&gt; combiner,             
                           Collection&lt;T&gt; collection) {
        return collection.stream()
                         .reduce(identity, reducer, combiner);
    }
[/code]

<h4><code>map</code>avec <code>reduce</code></h4>

Avant:

[code language="java"]
    static &lt;T, R&gt; List&lt;R&gt; map(Function&lt;T, R&gt; mapper, 
                              Collection&lt;T&gt; collection) {
        List&lt;R&gt; accumulator = new ArrayList&lt;&gt;();
        for (T element: collection) {
            accumulator.add(mapper.apply(element));
        }
        return accumulator;
    }
[/code]

Après:

[code language="java"]
    static &lt;T, U, R extends Collection&lt;U&gt;&gt; R map(
                   Function&lt;T, U&gt; mapper, 
                   R result, 
                   Collection&lt;T&gt; collection) {
        BiFunction&lt;R, T, R&gt; mapReducer = 
           (accumulator, item) -&gt; {
               accumulator.add(mapper.apply(item));
               return accumulator;
           };
        return reduce(mapReducer, result, 
                      CollectionUtils::addAll, collection);
    }
[/code]

Dans cette nouvelle version de <code>map</code> on remplace la boucle par un appel à la fonction <code>reduce</code>.
Pour appliquer cette fonction nous devons avoir:

<ul>
<li>Une <i> fonction de réduction </i> (<code>mapReducer</code>) qui applique la transformation (<code>mapper</code>) à l'élément en entrée et ajoute l'élément transformé à la collection en cours de réduction (<code>result</code>).</li>
<li>Une collection qui sera remplie par la réduction et qui contiendra les éléments transformés (<code>result</code>);</li>
</ul>

Le paramètre <code>combiner</code> de <code>reduce</code> est implémenté par la méthode <code>addAll</code> qui fusionne deux collections en une.

[code language="java"]
    static &lt;T, R extends Collection&lt;T&gt;&gt; R addAll(R c1, R c2) {
        c1.addAll(c2);
        return c1;
    }
[/code]

<h4><code>filter</code> avec <code>reduce</code></h4>

Avant:

[code language="java"]
    static  &lt;T&gt; List&lt;T&gt; filter(Predicate&lt;T&gt; predicate, 
                               Collection&lt;T&gt; collection) {
        List&lt;T&gt; accumulator = new ArrayList&lt;&gt;();
        for(T element: collection) {
            if (predicate.test(element)) {
                accumulator.add(element);
            }
        }
        return accumulator;
    }
[/code]

Après:

[code language="java"]
    static &lt;T, R extends Collection&lt;T&gt;&gt; R filter(
                         Predicate&lt;T&gt; predicate, 
                         R result, 
                         Collection&lt;T&gt; collection) {
        BiFunction&lt;R, T, R&gt; filterReducer = 
          (accumulator, item) -&gt; {
            if (predicate.test(item)) {
                accumulator.add(item);
            }
            return accumulator;
          };
        return reduce(filterReducer, result,
                      CollectionUtils::addAll, collection);
    }
[/code]

Dans cette nouvelle version de <code>filter</code> on remplace la boucle par un appel à la fonction <code>reduce</code>.
Pour appliquer cette fonction nous devons avoir:

<ul>
<li>Une <i>fonction de réduction </i> (<code>filterReducer</code>) qui ajoute l'élément en entrée si il est sélectionné (<code>predicate</code>) à la collection en cours de réduction (<code>result</code>).</li>
<li>Une collection qui sera remplie par la réduction et qui contiendra les éléments sélectionnés (<code>result</code>);</li>
</ul>

<h4>Implémentons <code>getAmountOf</code> avec les nouvelles fonctions <code>map</code> et <code>filter</code></h4>

Avant:

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = 
            filter(invoice -&gt; invoice.isDueFor(month), invoices());
        List&lt;Amount&gt; includeVATAmounts =
            map(Invoice::totalIncludingVAT, invoicesOfTheMonth);
        return reduce(Amount.ZERO, Amount::add, Amount::add,
                      includeVATAmounts);
    }
[/code]

Après:

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = 
             filter(invoice -&gt; invoice.isDueFor(month), 
                    new ArrayList&lt;&gt;(), invoices());
        List&lt;Amount&gt; includeVATAmounts =            
             map(Invoice::totalIncludingVAT, 
                    new ArrayList&lt;&gt;(), invoicesOfTheMonth);
        return reduce(Amount::add, Amount.ZERO, Amount::add,
                      includeVATAmounts);
    }
[/code]

Pas de gros changement si ce n'est la création des listes résultats lors des appels de <code>filter</code> et de <code>map</code>.

Au passage nous venons de voir comme construire <code>map</code>et <code>filter</code> à partir de <code>reduce</code>.

<h3>Deuxième étape: sortons maintenant <code>reduce</code> des implémentations de <code>map</code> et <code>filter</code></h3>

Lors de l'étape précédente le fait de construire <code>map</code> et <code>filter</code> avec <code>reduce</code> nous a permis de définir les <i> fonctions de réduction </i> qui permettent d'implémenter un <code>map</code> (<code>mapReducer</code>) et un <code>filter</code> (<code>filterReducer</code>).
Mais cette implémentation nous oblige aussi à avoir des collections intermédiaires.
Nous allons donc sortir <code>reduce</code> des implémentations de <code>map</code> et <code>filter</code>.

<h4><code>map</code> devient uniquement une fabrique de <i> fonction de réduction </i></h4>

Avant:

[code language="java"]
    static &lt;T, U, R extends Collection&lt;U&gt;&gt; R map(
                    Function&lt;T, U&gt; mapper, 
                    R result, 
                    Collection&lt;T&gt; collection) {
        BiFunction&lt;R, T, R&gt; mapReducer = 
          (accumulator, item) -&gt; {
            accumulator.add(mapper.apply(item));
            return accumulator;
          };
        return reduce(mapReducer, result, 
                      CollectionUtils::addAll, collection);
    }
[/code]

Après:

[code language="java"]
    static &lt;T, U, R extends Collection&lt;U&gt;&gt; BiFunction&lt;R, T, R&gt;
                                       map(Function&lt;T, U&gt; mapper) {
        return (accumulator, item) -&gt; {
            accumulator.add(mapper.apply(item));
            return accumulator;
        };
    }
[/code]

Dans cette version <code>map</code> devient une fabrique de <i> fonction de réduction </i> pour ajouter des éléments transformés dans la collection résultat.
Elle ne dépend plus des éléments à transformer, ni de la création de la collection résultat.
Elle a en paramètre uniquement la fonction de transformation (<code>mapper</code>).

<h4><code>filter</code> devient uniquement une fabrique de <i> fonction de réduction </i></h4>

Avant:

[code language="java"]
    static &lt;T, R extends Collection&lt;T&gt;&gt; R filter(
                                   Predicate&lt;T&gt; predicate, 
                                   R result, 
                                   Collection&lt;T&gt; collection) {
        BiFunction&lt;R, T, R&gt; filterReducer = 
          (accumulator, item) -&gt; {
            if (predicate.test(item)) {
                accumulator.add(item);
            }
            return accumulator;
          };
        return reduce(filterReducer, result,
                      CollectionUtils::addAll, collection);
    }
[/code]

Après:

[code language="java"]
    static &lt;T, R extends Collection&lt;T&gt;&gt; BiFunction&lt;R, T, R&gt;
                                   filter(Predicate&lt;T&gt; predicate) {
        return (R accumulator, T item) -&gt; {
            if (predicate.test(item)) {
                accumulator.add(item);
            }
            return accumulator;
        };
    }
[/code]

Dans cette version <code>filter</code> devient une fabrique de <i> fonction de réduction </i> pour sélectionner les éléments correspondant au prédicat dans la collection résultat.
Elle ne dépend plus des éléments à sélectionner, ni de la création de la collection résultat.
Elle a en paramètre uniquement le prédicat à appliquer (<code>predicate</code>).

<h4>Implémentons <code>getAmountOf</code> avec les nouvelles fonctions <code>map</code> et <code>filter</code></h4>

Avant:

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = 
               filter(invoice -&gt; invoice.isDueFor(month), 
                      new ArrayList&lt;&gt;(), invoices());
        List&lt;Amount&gt; includeVATAmounts =
               map(Invoice::totalIncludingVAT, 
                     new ArrayList&lt;&gt;(), invoicesOfTheMonth);
        return reduce(Amount::add, Amount.ZERO, Amount::add, 
                      includeVATAmounts);
    }
[/code]

Après:

[code language="java"]
    Amount getAmountOf(Month month) {
        List&lt;Invoice&gt; invoicesOfTheMonth = 
              reduce(filter(invoice -&gt; invoice.isDueFor(month)),        
                            new ArrayList&lt;&gt;(), 
                            CollectionUtils::addAll, invoices());
        List&lt;Amount&gt; includeVATAmounts =
             reduce(map(Invoice::totalIncludingVAT), 
                        new ArrayList&lt;&gt;(),
                        CollectionUtils::addAll,
                        invoicesOfTheMonth);
        return reduce(Amount::add, Amount.ZERO, Amount::add,
                      includeVATAmounts);
    }
[/code]

Maintenant que nous avons sorti <code>reduce</code> de <code>map</code> et <code>filter</code> nous allons supprimer les <code>reduce</code> intermédiaires et donc les collections intermédiaires.

<h3>Troisième étape: supprimons les listes intermédiaires</h3>

Nous souhaitons obtenir l'implémentation de <code>getAmountOf</code> suivante:

[code language="java"]
    Amount getAmountOf(Month month) {
        return reduce(Amount.ZERO,
                (filter((Invoice invoice) -&gt; 
                                invoice.isDueFor(month))  
                     .andThen(map(Invoice::totalIncludingVAT))) 
                     .apply(Amount::add), 
                Amount::add, invoices());
    }
[/code]

Pour rappel la fonction <code>reduce</code> prend en paramètre une <i> fonction de réduction </i> qui a la signature suivante:

[code language="java"]
    R reducer(R accumulator, T element)
[/code]

Il faut donc que la fonction créée par l'application de <code>Amount::add</code> à la composition de fonction soit du même type que <code>reducer</code>.

Dans notre exemple on a <code>Amount::add</code> qui est une fonction avec la signature suivante:

[code language="java"]
    BiFonction&lt;Amount, Amount, Amount&gt;
[/code]

Pour réduire une collection de factures (<code>Invoice</code>) en montant (<code>Amount</code>) il faut une fonction avec la signature suivante:

[code language="java"]
    BiFonction&lt;Amount, Invoice, Amount&gt;
[/code]

Il faut donc que la méthode <code>apply</code> ait la signature suivante:

[code language="java"]
    BiFonction&lt;Amount, Invoice, Amount&gt; apply(
                  BiFunction&lt;Amount, Amount, Amount&gt; nextReducer)
[/code]

Si on généralise on obtient la signature suivante:

[code language="java"]
    BiFunction&lt;R, U, R&gt; apply(BiFunction&lt;R, T, R&gt; nextReducer)
[/code]

Avec:

[code language="java"]
    R &lt;=&gt; Amount
    U &lt;=&gt; Invoice
    T &lt;=&gt; Amount
[/code]

La fonction <code>apply</code> permet de créer une chaîne de <i> fonctions de réduction </i>, c'est ce qu'on appelle un <b><code>Transducer</code></b>.

[code language="java"]
    abstract class Transducer&lt;T, U&gt; {
 
        private Transducer() {}

        abstract &lt;R&gt; BiFunction&lt;R, U, R&gt; apply(
                                 BiFunction&lt;R, T, R&gt; nextReducer);

        ...
    }
[/code]

Pour sélectionner les factures à payer il faut une fonction <code>filter</code> qui fabrique un <code>Transducer</code>:

[code language="java"]
    filter((Invoice invoice) -&gt; invoice.isDueFor(month))

    static &lt;V&gt; Transducer&lt;V, V&gt; filter(Predicate&lt;V&gt; predicate) {
        Objects.requireNonNull(predicate);
        return new Transducer&lt;V, V&gt;() {
            @Override
            public &lt;R&gt; BiFunction&lt;R, V, R&gt; apply(
                                BiFunction&lt;R, V, R&gt; nextReducer) {
                return (accumulator, input) -&gt; {
                    if (predicate.test(input)) {
                        return nextReducer
                                   .apply(accumulator, input);
                    }
                    return accumulator;
                };
            }
        };
    }
[/code]

Quand la <i> fonction de réduction </i> est appliquée, le prédicat est testé et si l'élément courant est sélectionné, la <i> fonction de réduction </i> suivante est appliquée avec l'accumulateur en cours (<code>accumulator</code>) et l'élément sélectionné (<code>input</code>) sinon l'accumulateur (<code>accumulator</code>) est retourné.

Pour transformer les factures à payer en montant toutes taxes comprises, il faut une fonction <code>map</code> qui crée un <b><code>Transducer</code></b>.

[code language="java"]
    map(Invoice::totalIncludingVAT)

    static &lt;T, U&gt; Transducer&lt;T, U&gt; map(Function&lt;U, T&gt; mapper) {
        Objects.requireNonNull(mapper);
        return new Transducer&lt;T, U&gt;() {
            @Override
            public &lt;R&gt; BiFunction&lt;R, U, R&gt; apply(
                                BiFunction&lt;R, T, R&gt; nextReducer) {
                return (accumulator, input) -&gt; nextReducer
                        .apply(accumulator, mapper.apply(input));
            }
        };
    }
[/code]

Quand la <i> fonction de réduction </i> est appliquée, la transformation (<code>mapper</code>) est appliquée sur l'élément à transformer et la <i> fonction de réduction </i> suivante est appliquée avec l'accumulateur en cours (<code>accumulator</code>) et l'élément transformé (<code>mapper.apply(input)</code>).

Pour pouvoir composer une fonction à partir des fonctions comme <code>filter</code> et <code>map</code> on ajoute la méthode <code>andThen</code> qui fabrique un nouveau <code>Transducer</code>.

[code language="java"]
    nextReducer -&gt; filter.apply(mapper.apply(nextReducer)

    &lt;V&gt; Transducer&lt;V, U&gt; andThen(Transducer&lt;V, T&gt; after) {
        Objects.requireNonNull(after);
        return new Transducer&lt;V, U&gt;() {
            @Override
            public &lt;R&gt; BiFunction&lt;R, U, R&gt; apply(
                               BiFunction&lt;R, V, R&gt; nextReducer) {
                return Transducer.this.apply(
                                   after.apply(nextReducer));
            }
        };
    }
[/code]

Quand la <i> fonction de réduction </i> est appliquée le <code>Transducer</code> <code>mapper</code> est créé en appliquant <code>after.apply(nextReducer)</code>, puis le <code>Transducer</code> <code>filter</code> est créé en appliquant le <code>Transducer</code> courant (<code>Transducer.this.apply(...)</code> avec le <code>Transducer</code> <code>mapper</code> en paramètre (créé par <code>after.apply(nextReducer)</code>).

On peut maintenant définir tous les <code>Transducers</code> que l'on souhaite:

[code language="java"]
    Transducer&lt;Invoice, Invoice&gt; t1 = filter(
                  (Invoice invoice) -&gt; invoice.isDueFor(month));

    Transducer&lt;Amount, Invoice&gt; t2 = map(
                  Invoice::totalIncludingVAT);

    Transducer&lt;Amount, Invoice&gt; t3 = t1.andThen(t2);
[/code]

Mais ce ne sont que des <i> définitions de fonction </i>. Elles créent une <i> fonction de réduction </i> que lorsque nous les appliquons avec en paramètre une autre <i> fonction de réduction </i>.

Par exemple si on veut la liste des factures dues:

[code language="java"]
    Transducer&lt;Invoice, Invoice&gt; t1 = filter(
            (Invoice invoice) -&gt; invoice.isDueFor(Month.OCTOBER));

    BiFunction&lt;List&lt;Invoice&gt;, Invoice, List&lt;Invoice&gt;&gt; reducer =
            t1.apply(CollectionUtils::add);

    assertThat(reduce(reducer, new ArrayList&lt;&gt;(),
            CollectionUtils::addAll, invoices()))                        
       .extracting(Invoice::totalIncludingVAT)
       .containsOnly(Amount.of(43.20), Amount.of(35.70));
[/code]

On voit ici que si on crée un <code>Transducer</code> (<code>t1</code>) pour sélectionner les factures dues, nous pouvons créer une <i> fonction de réduction </i> (<code>reducer</code>). Si nous appelons, <code>reduce</code> avec la fonction <code>reducer</code>, nous obtenons la liste des factures dues.

Nous venons donc de voir que les <code>Transducers</code> sont des <i>définitions de fonction </i> qui peuvent être utilisées seules ou composées de plusieurs <code>Transducers</code>. Nous pouvons appliquer n'importe quelle <i> fonction de réduction </i> au <code>Transducer</code> pour créer un <code>reducer</code>. Cette technique permet de réutiliser les <code>Transducers</code> et de les tester séparément. Un <code>Reducer</code> peut être passé en paramètre à n'importe quelle implémentation de <code>reduce</code> ou équivalent et les éléments à réduire peuvent provenir aussi bien d'une collection que de flux d'une socket, d'un fichier, etc... Cela permet de marier les <code>Transducers</code> avec la programmation réactive par exemple, mais ceci peut faire l'objet d'un article à part entière.

Si vous souhaitez retrouver les sources de cet article, elles sont disponibles sur mon 
<a href="https://github.com/PatrickGIRY/transducers" target="_blank">Github</a>.
