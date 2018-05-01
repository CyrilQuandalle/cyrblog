---
layout: post
title: "Java Enums: You have grace, elegance and power and this is what I Love!"
date: 2012-09-07 12:34
author: cyrille martraire
comments: true
categories: [clean, dsl, enum, hack, immutable, interface, java, object, optimization, polymorphism, Programmation, programming, singleton, state, strategy, syntax, valueobject]
---
<p style="text-align: justify;">While Java 8 is coming, are you sure you know well the enums that were introduced in Java 5? Java enums are still underestimated, and it’s a pity since they are more useful than you might think, they’re not just for your usual enumerated constants!</p>

<h1 style="text-align: justify;">Java enum is polymorphic</h1>

<p style="text-align: justify;">Java enums are real classes that can have behavior and even data.</p>

<p style="text-align: justify;">Let’s represent the <a href="http://en.wikipedia.org/wiki/Rock-paper-scissors" target="_blank">Rock-Paper-Scissors</a> game using an enum with a single method. Here are the unit tests to define the behavior:</p>

<pre>@Test
public void paper_beats_rock() {
    assertThat(PAPER.beats(ROCK)).isTrue();
    assertThat(ROCK.beats(PAPER)).isFalse();
}
@Test
public void scissors_beats_paper() {
    assertThat(SCISSORS.beats(PAPER)).isTrue();
    assertThat(PAPER.beats(SCISSORS)).isFalse();
}
@Test
public void rock_beats_scissors() {
    assertThat(ROCK.beats(SCISSORS)).isTrue();
    assertThat(SCISSORS.beats(ROCK)).isFalse();
}</pre>

<div style="text-align: justify;">And here is the implementation of the enum, that primarily relies on the ordinal integer of each enum constant, such as the item N+1 wins over the item N. This equivalence between the enum constants and the integers is quite handy in many cases.</div>

<pre>/** Enums have behavior! */
public enum Gesture {
    ROCK() {
        // Enums are polymorphic, that's really handy!
        @Override
        public boolean beats(Gesture other) {
            return other == SCISSORS;
        }
    },
    PAPER, SCISSORS;

    // we can implement with the integer representation
    public boolean beats(Gesture other) {
        return ordinal() - other.ordinal() == 1;
    }
}</pre>

<p style="text-align: justify;">Notice that there is not a single IF statement anywhere, all the business logic is handled by the integer logic and by the polymorphism, where we override the method for the ROCK case. If the ordering between the items was not cyclic we could implement it just using the natural ordering of the enum, here the polymorphism helps deal with the cycle.</p>

<div style="text-align: justify;">
<div id="attachment_3411" class="wp-caption alignnone" style="width: 310px;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/banner_antiif_campaign.png">
<img class="size-full wp-image-3411" title="banner_antiif_campaign" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/banner_antiif_campaign.png" alt="" width="300" height="120" /></a>
<p class="wp-caption-text">You can do it without any IF statement! Yes you can!</p>

</div>
</div>

<p style="text-align: justify;">This Java enum is also a perfect example that you can have your cake (offer a nice object-oriented API with intent-revealing names), and eat it too (implement with simple and efficient integer logic like in the good ol’ days).</p>

<p style="text-align: justify;">Over my last projects I’ve used a lot enums as a substitute for classes: they are guaranted to be singleton, have ordering, hashcode, equals and serialization to and from text all built-in, without any clutter in the source code.</p>

<p style="text-align: justify;">If you’re looking for Value Objects and if you can represent a part of your domain with a limited set of instances, then the enum is what you need! It’s a bit like the <a href="http://www.scala-lang.org/node/123" target="_blank">Sealed Case Class in Scala</a>, except it’s totally restricted to a set of instances all defined at compile time. The bounded set of instances at compile-time is a real limitation, but now with <a href="http://continuousdelivery.com/" target="_blank">continuous delivery</a>, you can probably wait for the next release if you really need one extra case.</p>

<h1 style="text-align: justify;">Well-suited for the Strategy pattern</h1>

<p style="text-align: justify;">Let’s move to to a system for the (in-)famous <a href="http://en.wikipedia.org/wiki/Eurovision_Song_Contest" target="_blank">Eurovision song contest</a>; we want to be able to configure the behavior on when to notify (or not) users of any new Eurovision event. It’s important. Let’s do that with an enum:</p>

<pre>/** The policy on how to notify the user of any Eurovision song contest event */
public enum EurovisionNotification {

    /** I love Eurovision, don't want to miss it, never! */
    ALWAYS() {
        @Override
        public boolean mustNotify(String eventCity, String userCity) {
            return true;
        }
    },

    /**
     * I only want to know about Eurovision if it takes place in my city, so
     * that I can take holidays elsewhere at the same time
     */
    ONLY_IF_IN_MY_CITY() {
        // a case of flyweight pattern since we pass all the extrinsi data as
        // arguments instead of storing them as member data
        @Override
        public boolean mustNotify(String eventCity, String userCity) {
            return eventCity.equalsIgnoreCase(userCity);
        }
    },

    /** I don't care, I don't want to know */
    NEVER() {
        @Override
        public boolean mustNotify(String eventCity, String userCity) {
            return false;
        }
    };

    // no default behavior
    public abstract boolean mustNotify(String eventCity, String userCity);

}</pre>

<p style="text-align: justify;">And a unit test for the non trivial case ONLY_IF_IN_MY_CITY:</p>

<pre>@Test
public void notify_users_in_Baku_only() {
    assertThat(ONLY_IF_IN_MY_CITY.mustNotify("Baku", "BAKU")).isTrue();
    assertThat(ONLY_IF_IN_MY_CITY.mustNotify("Baku", Paris")).isFalse();
}</pre>

<p style="text-align: justify;">Here we define the method <strong>abstract</strong>, only to implement it for each case. An alternative would be to implement a <strong>default behavior</strong> and only override it for each case when it makes sense, just like in the Rock-Paper-Scissors game.</p>

<p style="text-align: justify;">Again we don’t need the switch on enum to choose the behavior, we rely on polymorphism instead. You probably don’t need the switch on enum much, except for dependency reasons. For example when the enum is part of a message sent to the outside world as in Data Transfer Objects (DTO), you do not want any dependency to your internal code in the enum or its signature.</p>

<p style="text-align: justify;">For the Eurovision strategy, using TDD we could start with a simple boolean for the cases ALWAYS and NEVER. It would then be promoted into the enum as soon as we introduce the third strategy ONLY_IF_IN_MY_CITY. Promoting primitives is also in the spirit of the 7th rule « <strong>Wrap all primitives</strong> » from the <a href="http://www.xpteam.com/jeff/writings/objectcalisthenics.rtf" target="_blank">Object Calisthenics</a>, and an enum is the perfect way to wrap a boolean or an integer with a bounded set of possible values.</p>

<p style="text-align: justify;">Because the strategy pattern is often controlled by configuration, the built-in serialization to and from String is also very convenient to store your settings.</p>

<h1 style="text-align: justify;">Perfect match for the State pattern</h1>

<p style="text-align: justify;">Just like the Strategy pattern, the Java enum is very well-suited for <strong>finite state machines</strong>, where by definition the set of possible states is finite.</p>

<div id="attachment_3434" class="wp-caption alignnone" style="width: 360px; text-align: justify;"><a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/snugfitseatsleepbig.jpeg"><img class="size-full wp-image-3434" title="eat_sleep_poop_cry_Baby_T-Shirt" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/snugfitseatsleepbig.jpeg" alt="" width="350" height="330" /></a>
<p class="wp-caption-text">A baby as a finite state machine (picture from www.alongcamebaby.ca)</p>

</div>

<p style="text-align: justify;">Let’s take the example of a baby simplified as a state machine, and make it an enum:</p>

<pre>/**
 * The primary baby states (simplified)
 */
public enum BabyState {

    POOP(null), SLEEP(POOP), EAT(SLEEP), CRY(EAT);

    private final BabyState next;

    private BabyState(BabyState next) {
        this.next = next;
    }

    public BabyState next(boolean discomfort) {
        if (discomfort) {
            return CRY;
        }
        return next == null ? EAT : next;
    }
}</pre>

<p style="text-align: justify;">And of course some unit tests to drive the behavior:</p>

<pre>@Test
public void eat_then_sleep_then_poop_and_repeat() {
    assertThat(EAT.next(NO_DISCOMFORT)).isEqualTo(SLEEP);
    assertThat(SLEEP.next(NO_DISCOMFORT)).isEqualTo(POOP);
    assertThat(POOP.next(NO_DISCOMFORT)).isEqualTo(EAT);
}

@Test
public void if_discomfort_then_cry_then_eat() {
    assertThat(SLEEP.next(DISCOMFORT)).isEqualTo(CRY);
    assertThat(CRY.next(NO_DISCOMFORT)).isEqualTo(EAT);
}</pre>

<p style="text-align: justify;">Yes we can reference enum constants between them, with the restriction that only constants defined before can be referenced. Here we have a cycle between the states EAT -&gt; SLEEP -&gt; POOP -&gt; EAT etc. so we need to open the <strong>cycle</strong> and close it with a workaround at runtime.</p>

<p style="text-align: justify;">We indeed have a <strong>graph</strong> with the CRY state that can be accessed from any state.</p>

<p style="text-align: justify;">I’ve already used enums to represent simple <strong>trees</strong> by categories simply by referencing in each node its elements, all with enum constants.</p>

<h1 style="text-align: justify;">Enum-optimized collections</h1>

<p class="p1" style="text-align: justify;">Enums also have the benefits of coming with their dedicated implementations for Map and Set: <em>EnumMap</em> and <em>EnumSet</em>.</p>

<p class="p1" style="text-align: justify;">These collections have the same interface and behave just like your regular collections, but internally they exploit the integer nature of the enums, as an optimization. In short you have old C-style data structures and idioms (bit masking and the like) hidden behind an elegant interface. This also demonstrate how you don’t have to compromise your API’s for the sake of efficiency!</p>

<p class="p1" style="text-align: justify;">To illustrate the use of these dedicated collections, let’s represent the 7 cards in Jurgen Appelo’s <a href="http://www.noop.nl/2011/03/delegation-poker-game-description.html" target="_blank">Delegation Poker</a>:</p>

<pre>public enum AuthorityLevel {

    /** make decision as the manager */
    TELL,

    /** convince people about decision */
    SELL,

    /** get input from team before decision */
    CONSULT,

    /** make decision together with team */
    AGREE,

    /** influence decision made by the team */
    ADVISE,

    /** ask feedback after decision by team */
    INQUIRE,

    /** no influence, let team work it out */
    DELEGATE;</pre>

<p style="text-align: justify;">There are 7 cards, the first 3 are more control-oriented, the middle card is balanced, and the 3 last cards are more delegation-oriented (I made that interpretation up, please refer to his book for explanations). In the Delegation Poker, every player selects a card for a given situation, and earns as many points as the card value (from 1 to 7), except the players in the « highest minority ».</p>

<p style="text-align: justify;">It’s trivial to compute the number of points using the ordinal value + 1. It is also straightforward to select the control oriented cards by their ordinal value, or we can use a Set built from a range like we do below to select the delegation-oriented cards:</p>

<pre>   public int numberOfPoints() {
        return ordinal() + 1;
    }

    // It's ok to use the internal ordinal integer for the implementation
    public boolean isControlOriented() {
        return ordinal() &lt; AGREE.ordinal();
    }

    // EnumSet is a Set implementation that benefits from the integer-like
    // nature of the enums
    public static Set DELEGATION_LEVELS = EnumSet.range(ADVISE, DELEGATE);

    // enums are comparable hence the usual benefits
    public static AuthorityLevel highest(List levels) {
        return Collections.max(levels);
    }
}</pre>

<p style="text-align: justify;">EnumSet offers convenient static factory methods like range(from, to), to create a set that includes every enum constant starting between ADVISE and DELEGATE in our example, in the declaration order.</p>

<p style="text-align: justify;">To compute the highest minority we start with the highest card, which is nothing but finding the max, something trivial since the enum is always comparable.</p>

<p style="text-align: justify;">Whenever we need to use this enum as a key in a Map, we should use the EnumMap, as illustrated in the test below:</p>

<pre>// Using an EnumMap to represent the votes by authority level
@Test
public void votes_with_a_clear_majority() {
    final Map&lt;AuthorityLevel, Integer&gt; votes = new EnumMap(AuthorityLevel.class);
    votes.put(SELL, 1);
    votes.put(ADVISE, 3);
    votes.put(INQUIRE, 2);
    assertThat(votes.get(ADVISE)).isEqualTo(3);
}</pre>

<h1 style="text-align: justify;">Java enums are good, eat them!</h1>

<p style="text-align: justify;">I love Java enums: they’re just perfect for Value Objects in the Domain-Driven Design sense where the set of every possible values is bounded. In a recent project I deliberatly managed to have a majority of value types expressed as enums. You get a lot of awesomeness for free, and especially with almost no technical noise. This helps improve my <strong>signal-to-noise ratio</strong> between the words from the domain and the technical jargon.</p>

<p style="text-align: justify;">Or course I make sure each enum constant is also <strong>immutable</strong>, and I get the correct equals, hashcode, toString, String or integer serialization, singleton-ness and very efficient collections on them for free, all that with very little code.</p>

<div id="attachment_3442" class="wp-caption alignnone" style="width: 470px; text-align: justify;">(picture from sys-con.com – Jim Barnabee article) »]<a href="http://cyrille.martraire.com/wp-content/uploads/2012/08/power_of_polymorphism.jpeg"><img class="size-full wp-image-3442 " title="power_of_polymorphism" src="http://cyrille.martraire.com/wp-content/uploads/2012/08/power_of_polymorphism.jpeg" alt="" width="460" height="460" /></a>
<p class="wp-caption-text">The power of polymorphism</p>

</div>

<p style="text-align: justify;">The enum polymorphism is very handy, and I never use <em>instanceof</em> on enums and I hardly need to switch on the enum either.</p>

<p style="text-align: justify;">I’d love that the Java enum is completed by a similar construct just like the case class in Scala, for when the set of possible values cannot be bounded. And a way to enforce immutability of any class would be nice too. Am I asking too much?</p>

<p style="text-align: justify;">Also &lt;troll&gt;don’t even try to compare the Java enum with the C# enum…&lt;/troll&gt;</p>
