---
layout: post
title: Pattern matching en Java 8
date: 2014-10-20 13:03
author: patrick giry
comments: true
categories: [Bonnes pratiques de dév, Fonctionnel, fonctionnel, java, java8, lambda, Programmation, programming]
---
Le filtrage par motif, en anglais <strong>pattern matching</strong>, consiste pour une valeur donnée à voir si elle correspond à un motif ou pas. Si c'est le cas une action est déclenchée.

De manière intrinsèque le langage Java possède la structure <code>switch ... case</code>.

On peut l'utiliser avec des entiers (byte, short et int):

[code language="java"]
    static String toGender(int genderCode) {
         final String gender;
         switch (genderCode) {
             case 1: gender = &quot;man&quot;;
                 break;
             case 2: gender = &quot;woman&quot;;
                 break;
             default:
                 throw new IllegalArgumentException(&quot;Unknown gender for code &quot; + genderCode);
         }
         return gender;
    }

    assertThat(toGender(1)).isEqualTo(&quot;man&quot;);
[/code]

On peut utiliser une énumération pour définir les constantes à tester:

[code language="java"]
    static String toGender(GenderCode genderCode) {
        final String gender;
        switch (genderCode) {
            case MAN: gender = &quot;man&quot;;
                break;
            case WOMAN: gender = &quot;woman&quot;;
                break;
            default:
                throw new IllegalArgumentException(&quot;Unknown gender for code &quot; + genderCode);
        }
        return gender;
    }

    static enum GenderCode {
        MAN,
        WOMAN
    }

    assertThat(toGender(GenderCode.MAN)).isEqualTo(&quot;man&quot;);
[/code]

On peut l'utiliser également avec des caractères:

[code language="java"]
    static String toCivility(char civilityCode) {
        final String civility;
        switch (civilityCode) {
            case 'M' : civility = &quot;Monsieur&quot;;
                break;
            case 'W' : civility =  &quot;Madame&quot;;
                break;
            default:
                throw new IllegalArgumentException(&quot;Unknown civility for code &quot; + civilityCode);
        }
        return civility;
    }

    assertThat(toCivility('M')).isEqualTo(&quot;Monsieur&quot;);
[/code]

Depuis le JDK 1.7 on peut l'utiliser également avec des chaînes de caractères:

[code language="java"]
     static String toCivility(String abbreviation) {
        final String civility;
        switch (abbreviation) {
            case &quot;Mr.&quot; : civility = &quot;Monsieur&quot;;
                break;
            case &quot;Mrs.&quot; : civility =  &quot;Madame&quot;;
                break;
            default:
                throw new IllegalArgumentException(&quot;Unknown civility for abbreviation &quot; + abbreviation);
        }
        return civility;
     }

     assertThat(toCivility(&quot;Mrs.&quot;)).isEqualTo(&quot;Madame&quot;);
[/code]

D'autres langages permettent de faire du <strong>pattern matching</strong> sur des choses plus complexes comme les types par exemple.
Nous allons donc voir comment implémenter le <strong>pattern matching</strong> et comment Java 8 nous permet de réduire le code nécessaire pour le réaliser.

Essayons d'implémenter un <strong>pattern matching</strong> en fonction du type de la valeur à tester. Il nous faut donc une méthode qui prend une valeur en entrée et nous retourne le résultat d'instruction si la valeur valide une condition (ici le type de la valeur) :

[code language="java"]
    public interface PatternMatching&lt;T, R&gt; {

        Optional&lt;R&gt; matches(V value);

    }
[/code]

Le JDK 8 possède une classe <code>java.util.Optional</code> qui permet d'indiquer que la réponse d'une méthode peut être le résultat attendu ou rien. Dans notre exemple si une valeur correspond à un type attendu la méthode <code>matches()</code> retournera un <code>Optional</code> qui contiendra le résultat de l'action exécutée par le <strong>pattern matching</strong>.

[code language="java"]
    assertThat(pm.matches(42)).isEqualTo(Optional.of(&quot;Integer: 42&quot;));
[/code]

Dans le cas contraire la méthode <code>matches()</code> retourner un <code>Optional.empty()</code>.

[code language="java"]
    assertThat(pm.matches(43.1)).isEqualTo(Optional.empty());
[/code]

Maintenant il nous faut pouvoir définir un <strong>pattern matching</strong> qui pour une condition donnée associe une action et que ce soit appelé par la méthode <code>matches()</code>.
Le JDK 8 permet de définir des méthodes statiques dans une interface. On va donc enrichir notre interface d'une méthode de fabrique. Elle prendra en paramètre un prédicat pour la condition et une fonction pour l'action associée:

[code language="java"]
    public interface PatternMatching&lt;T, R&gt; {

        static &lt;T, R&gt; PatternMatching&lt;T, R&gt; when(Predicate&lt;T&gt; predicate, Function&lt;T, R&gt; action) { ... }

        Optional&lt;R&gt; matches(V value);

    }
[/code]

Les interfaces <code>java.util.function.Predicate</code> et <code>java.util.function.Function</code> sont fournies par le JDK 8 pour définir respectivement des prédicats et des fonctions.
On peut appeler maintenant cette méthode:

[code language="java"]
    final PatternMatching&lt;? super Number, String&gt; pm =
                    when(new Predicate&lt;Number&gt;() {
                        @Override
                        public boolean test(Number number) {
                            return Integer.class.isInstance(number);
                        }
                    }, new Function&lt;Number, String&gt;() {
                        @Override
                        public String apply(Number x) {
                            return &quot;Integer: &quot; + x;
                        }
                    });

    assertThat(pm.matches(42)).isEqualTo(Optional.of(&quot;Integer: 42&quot;));
[/code]

Avec le JDK 8 on peut simplifier cet appel en utilisant une référence de méthode pour tester que la valeur est une instance d'<code>Integer</code> et une expression lambda pour définir l'action à exécuter si le prédicat est vrai.

[code language="java"]
    final PatternMatching&lt;? super Number, String&gt; pm =
                when(Integer.class::isInstance, x -&gt; &quot;Integer: &quot; + x);

    assertThat(pm.matches(42)).isEqualTo(Optional.of(&quot;Integer: 42&quot;));
[/code]

Le <strong>pattern matching</strong> peut être créé et défini à partir d'une expression lambda:

[code language="java"]
    public interface PatternMatching&lt;T, R&gt; {

        static &lt;T, R&gt; PatternMatching&lt;T, R&gt; when(Predicate&lt;T&gt; predicate, Function&lt;T, R&gt; action) {
            Objects.requireNonNull(predicate);
            Objects.requireNonNull(action);

            return value -&gt; {
                if (predicate.test(value)) {
                    return Optional.of(action.apply(value));
                } else {
                    return Optional.empty();
                }
            };
        }

        Optional&lt;R&gt; matches(V value);
    }

[/code]

Après s'être assurés que le prédicat et l'action sont bien définis, on crée une lambda qui prend la valeur en argument de la méthode <code>matches()</code>, appelle le prédicat puis l'action si celui-ci est vrai. On met le résultat de l'action dans un <code>Optional</code>.
L'action ne peut pas retourner de <code>null</code>, c'est grâce à ça que l'on sait si une condition a été remplie et que l'action correspondante a été exécutée.
Si l'action doit retourner malgré tout <code>null</code> il faut utiliser le design pattern <code>Null Object</code> ou définir le résultat comme un <code>Optional</code>.

[code language="java"]
    final PatternMatching&lt;? super Number, Optional&lt;String&gt;&gt; pm =
                    when(Integer.class::isInstance, x -&gt; Optional.&lt;String&gt;empty());

    assertThat(pm.matches(42)).isEqualTo(Optional.of(Optional.empty()));
[/code]

Si le prédicat est faux un <code>Optional.empty()</code> est retourné.

[code language="java"]
    PatternMatching&lt;? super Number, String&gt; pm =
                    when(Integer.class::isInstance, x -&gt; &quot;Integer: &quot; + x);

    assertThat(pm.matches(43.1)).isEqualTo(Optional.empty());
[/code]

On veut pouvoir définir plusieurs conditions dans notre <strong>pattern matching</strong> comme ceci :

[code language="java"]
    final PatternMatching&lt;? super Number, String&gt; pm =
                when(Integer.class::isInstance, x -&gt; &quot;Integer: &quot; + x)
                .orWhen(Double.class::isInstance, x -&gt; &quot;Double: &quot; + x);

    assertThat(pm.matches(42)).isEqualTo(Optional.of(&quot;Integer: 42&quot;));
    assertThat(pm.matches(1.42)).isEqualTo(Optional.of(&quot;Double: 1.42&quot;));
[/code]

En JDK 8 on peut définir des implémentations de méthode par défaut dans une interface. C'est ce qu'on utilise pour <code>orWhen()</code>.

[code language="java"]
    public interface PatternMatching&lt;T, R&gt; {
           ...

         default PatternMatching&lt;T, R&gt; orWhen(Predicate&lt;T&gt; predicate, Function&lt;T, R&gt; action) {
             Objects.requireNonNull(predicate);
             Objects.requireNonNull(action);

             return value -&gt; {
                 final Optional&lt;R&gt; result = matches(value);
                 if (result.isPresent()) {
                     return result;
                 } else {
                     if (predicate.test(value)) {
                         return Optional.of(action.apply(value));
                     } else {
                         return Optional.empty();
                     }
                 }
             };

         }
           ...
    }
[/code]

Pour faire appelle à <code>orWhen()</code> on doit avoir déjà une instance de <code>PatternMatching</code>, créée par <code>when()</code> par exemple. C'est pour cette raison que la lambda qui implémente <code>matches()</code> dans <code>orWhen()</code> appelle <code>matches(value)</code> pour voir si le motif précédent a déclenché une action.
Si aucune action n'a été déclenchée on appelle le prédicat du second motif, et s'il est vrai on déclenche l'action associée.

On complète pour pouvoir déclencher une action si aucun motif ne correspond à la valeur.

[code language="java"]
    public interface PatternMatching&lt;T, R&gt; {
           ...

         default PatternMatching&lt;T, R&gt; otherwise(Function&lt;T, R&gt; action) {
             Objects.requireNonNull(action);

             return value -&gt; {
                 final Optional&lt;R&gt; result = matches(value);
                 if (result.isPresent()) {
                     return result;
                 } else {
                     return Optional.of(action.apply(value));
                 }
             };
         }
           ...
    }
[/code]

Si on appelle <code>otherwise()</code> en dernière position le contrat est rempli.

[code language="java"]
    PatternMatching&lt;? super String, String&gt; pm = when(x-&gt; false, x -&gt; &quot;&quot;)
                    .otherwise(x -&gt; &quot;got this object: &quot; + x);

    assertThat(pm.matches(&quot;foo&quot;)).isEqualTo(Optional.of(&quot;got this object: foo&quot;));
[/code]

Voilà on vient d'implémenter un <strong>pattern matching</strong> en 60 lignes environ :

[code language="java"]
    import java.util.Objects;
    import java.util.Optional;
    import java.util.function.Function;
    import java.util.function.Predicate;

    @FunctionalInterface
    public interface PatternMatching&lt;T, R&gt; {

        static &lt;T, R&gt; PatternMatching&lt;T, R&gt; when(Predicate&lt;T&gt; predicate, Function&lt;T, R&gt; action) {
            Objects.requireNonNull(predicate);
            Objects.requireNonNull(action);

            return value -&gt; {
                if (predicate.test(value)) {
                    return Optional.of(action.apply(value));
                } else {
                    return Optional.empty();
                }
            };
        }

        default PatternMatching&lt;T, R&gt; otherwise(Function&lt;T, R&gt; action) {
            Objects.requireNonNull(action);

            return value -&gt; {
                final Optional&lt;R&gt; result = matches(value);
                if (result.isPresent()) {
                    return result;
                } else {
                    return Optional.of(action.apply(value));
                }
            };
        }

        default PatternMatching&lt;T, R&gt; orWhen(Predicate&lt;T&gt; predicate, Function&lt;T, R&gt; action) {
            Objects.requireNonNull(predicate);
            Objects.requireNonNull(action);

            return value -&gt; {
                final Optional&lt;R&gt; result = matches(value);
                if (result.isPresent()) {
                    return result;
                } else {
                    if (predicate.test(value)) {
                        return Optional.of(action.apply(value));
                    } else {
                        return Optional.empty();
                    }
                }
            };

        }

        Optional&lt;R&gt; matches(T value);

    }
[/code]

On peut l'utiliser avec des chaînes de caractères:

[code language="java"]
    PatternMatching&lt;? super String, String&gt; pm = when(&quot;world&quot;::equals, x -&gt; &quot;Hello, &quot; + x);

    assertThat(pm.matches(&quot;world&quot;)).isEqualTo(Optional.of(&quot;Hello, world&quot;));

    assertThat(pm.matches(&quot;Bob&quot;)).isEqualTo(Optional.empty());
[/code]

On peut l'utiliser avec des entiers:

[code language="java"]
    PatternMatching&lt;? super Integer, String&gt; pm = when(x -&gt; x == 42, x -&gt; &quot;forty-two&quot;);

    assertThat(pm.matches(42)).isEqualTo(Optional.of(&quot;forty-two&quot;));

    assertThat(pm.matches(43)).isEqualTo(Optional.empty());
[/code]

On peut faire des combinaisons:

[code language="java"]
    PatternMatching&lt;Object, String&gt; pm = when(&quot;world&quot;::equals, x -&gt; &quot;Hello, &quot; + x)
                   .orWhen(Double.class::isInstance, x -&gt; &quot;Double: &quot; + x)
                   .otherwise(x -&gt; &quot;got this object: &quot; + x);

    assertThat(pm.matches(&quot;world&quot;)).isEqualTo(Optional.of(&quot;Hello, world&quot;));
    assertThat(pm.matches(&quot;foo&quot;)).isEqualTo(Optional.of(&quot;got this object: foo&quot;));
    assertThat(pm.matches(1.42)).isEqualTo(Optional.of(&quot;Double: 1.42&quot;));
[/code]

J'espère vous avoir convaincu que faire du <strong>pattern matching</strong> en Java 8 n'est pas une difficulté insurmontable, et c'est au passage l'occasion de jouer avec les nouveautés de Java 8 !
