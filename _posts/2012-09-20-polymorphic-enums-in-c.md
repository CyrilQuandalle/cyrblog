---
layout: post
title: Polymorphic enums in C# ?
date: 2012-09-20 09:39
author: pierre irrmann
comments: true
categories: [C#, enum, polymorphism, Programmation, syntax]
---
<h3>Using Java as a inspiration source for C# (<em>hmm wait, hasn’t that already been done from the start ?</em>)</h3>

<img style="margin: 0px 0px 0px 20px; float: right;" src="http://www.pirrmann.net/wp-content/uploads/2012/09/gestures.jpg" alt="" align="right" />

<p align="justify">A few weeks ago, I read this post by @cyriux, describing how you could use polymorphic enums in Java. Actually, I had already heard him telling that enums in C# were nothing like the ones in Java. And even if he’s sometimes trolling, he’s really got a point there. Then I thought to myself : I know I won’t be able to add this syntax to C#, because I can’t change the compiler, but how clause could I get to polymorphic enums ? Let’s try that…</p>

First, what <strong>are</strong> the common characteristics of an enum ?

<ul>
    <li>enums bind together a scalar value and a string representation, so enums can be converted to and parsed from strings</li>
    <li>for the same reason, enums can be casted to and from scalar values (int, long, short…),</li>
    <li>enums should be immutable (in fact, unless you introduce some mutable state in them, they already are),</li>
    <li>enums cannot be extended by other types : once defined, their set of values is closed and cannot be modified.</li>
</ul>

<p style="text-align: justify;">I haven’t checked for Java, but here are some other characteristics of C# enums :</p>

<ul style="text-align: justify;">
    <li>enums are a value types,</li>
    <li>enums implement the following interfaces : <a href="https://msdn.microsoft.com/fr-fr/library/system.icomparable(v=vs.110).aspx" target="_blank">IComparable</a>, <a href="https://msdn.microsoft.com/fr-fr/library/system.iformattable(v=vs.110).aspx" target="_blank">IFormattable</a>, <a href="http://msdn.microsoft.com/en-us/library/system.iconvertible.aspx" target="_blank">IConvertible</a></li>
</ul>

<p style="text-align: justify;">In order to build a type that behaves like an enum, I’ve made the following decisions :</p>

<ul style="text-align: justify;">
    <li>I want to build a base type from which actual enums will derive. As there is nothing in C# as an abstract struct (value type), I have to implement a class and not a struct.</li>
    <li>In order to obtain a value type behaviour (value comparison instead of instance equality), I’ll will have to implement it.</li>
    <li>I will implement interfaces <a href="https://msdn.microsoft.com/fr-fr/library/system.icomparable(v=vs.110).aspx" target="_blank">IComparable</a> and <a href="http://msdn.microsoft.com/en-us/library/system.iconvertible.aspx" target="_blank">IConvertible</a>, but I haven’t really seen any use case where I would use <a href="https://msdn.microsoft.com/fr-fr/library/system.iformattable(v=vs.110).aspx" target="_blank">IFormattable</a>, so I chose not to implement it.</li>
    <li>Enums in C# don’t have an Ordinal property, but I’ll introduce one, so that I can write unit tests as close as possible as @cyriux’s ones. It will look a little bit Java-ish… but of course I’ll keep the C# casing convention.</li>
    <li>The underlying type used to represent an enum is an integer by default, but other types can be used, such as a long or an unsigned short. I will try to make the enums definitions as short a possible, so I’ll use generics and take advantage of the type inference system.</li>
</ul>

<h3 style="text-align: justify;">My C# enum has behaviour</h3>

<p style="text-align: justify;">Enough text there ! Directly inspired from @cyriux’s post’s first sample “<a href="http://en.wikipedia.org/wiki/Rock-paper-scissors" target="_blank">Rock - paper - scissors</a>”, here is the test cases that I want to execute :</p>

<pre>[TestMethod]
public void paper_beats_rock()
{
    Assert.IsTrue(Gesture.PAPER.Beats(Gesture.ROCK));
    Assert.IsFalse(Gesture.ROCK.Beats(Gesture.PAPER));
}

[TestMethod]
public void scissors_beats_paper()
{
    Assert.IsTrue(Gesture.SCISSORS.Beats(Gesture.PAPER));
    Assert.IsFalse(Gesture.PAPER.Beats(Gesture.SCISSORS));
}

[TestMethod]
public void rock_beats_scissors()
{
    Assert.IsTrue(Gesture.ROCK.Beats(Gesture.SCISSORS));
    Assert.IsFalse(Gesture.SCISSORS.Beats(Gesture.ROCK));
}</pre>

<p style="text-align: justify;">And here is the implementation of the enum, built upon my PolymorphicEnum class :</p>

<pre>/** PolymorphicEnum have behavior! **/
public class Gesture : PolymorphicEnum&lt;Gesture&gt;
{
    public static Gesture ROCK = Register&lt;RockGesture&gt;();
    public static Gesture PAPER = Register();
    public static Gesture SCISSORS = Register();

    // we can implement with the integer representation
    public virtual bool Beats(Gesture other)
    {
        return this.Ordinal - other.Ordinal == 1;
    }

    private class RockGesture : Gesture
    {
        public override bool Beats(Gesture other)
        {
            return other == SCISSORS;
        }
    }
}</pre>

<p style="text-align: justify;">As you can see in the implementation, it is now possible to define a Gesture enum, that exposes 3 static properties of type Gesture. One of these properties, ROCK, is in fact an instance of RockGesture, which is a Gesture that overrides the behaviour of Beats method.</p>

<h3 style="text-align: justify;">Strategy Pattern</h3>

<p style="text-align: justify;">All the Java sample from the post can now be ported to C# ! The next one was the Eurovision contest, where an abstract method was defined in the base enum, and overridden in each enum instance, in order to implement a strategy pattern. This is where I admit I couldn’t get the same behaviour, as for internal implementations reasons I needed my base class to not be abstract… So I had to define a default behaviour :</p>

<pre>public class EurovisionNotification
    : PolymorphicEnum&lt;EurovisionNotification&gt;
{
    /** Default behavior : I don't want to know ! */
    public virtual bool MustNotify(String eventCity,
                                   String userCity)
    {
        return false;
    }

    /** I love Eurovision, don't want to miss it, never! */
    private class EurovisionNotificationAlways
        : EurovisionNotification
    {
        public override bool MustNotify(string eventCity,
                                        string userCity)
        {
             return true;
        }
    }

    /**
     * I only want to know about Eurovision if it takes place
     * in my city, so that I can take holidays elsewhere at
     * the same time
     */
    private class EurovisionNotificationOnlyCity
        : EurovisionNotification
    {
        public override bool MustNotify(string eventCity,
                                        string userCity)
        {
             return eventCity.Equals(
                userCity,
                StringComparison.InvariantCultureIgnoreCase);
        }
    }

    public static EurovisionNotification ALWAYS =
        Register&lt;EurovisionNotificationAlways&gt;();
    public static EurovisionNotification ONLY_IN_MY_CITY =
        Register&lt;EurovisionNotificationOnlyCity&gt;();
    public static EurovisionNotification NEVER =
        Register();
}</pre>

<h3 style="text-align: justify;">State Pattern</h3>

<p style="text-align: justify;">The next sample is the one where a baby is represented as a state machine :</p>

<pre>public class BabyState : PolymorphicEnum&lt;BabyState&gt;
{
    public static BabyState POOP = Register();

    public static BabyState SLEEP =
        Register(data: new { Next = POOP });

    public static BabyState EAT =
        Register(data: new { Next = SLEEP });

    public static BabyState CRY =
        Register(data: new { Next = EAT });

    public BabyState Next(bool discomfort)
    {
        if (discomfort)
            return CRY;

        return this.Data == null
            ? EAT
            : (BabyState)((dynamic)this.Data).Next;
    }
}</pre>

<p style="text-align: justify;"><img style="float: right;" src="http://www.pirrmann.net/wp-content/uploads/2012/09/baby-states.png" alt="" align="right" /></p>

<p style="text-align: justify;">Just as for the Java enums, “we can reference enum constants between them, with the restriction that only constants defined before can be referenced”. The workaround here, as @cyriux wrote in his post, is to open the cycle. This builds allows us to build the graph on the right.</p>

<p style="text-align: justify;">We can notice here that in order to embed data in an enum value, I have introduced an extra parameter “data” in the Enum method, which is responsible for the Enum values registration. Using this extra parameter, we can go back to the Eurovision sample and rewrite this way :</p>

<pre>using Policy = System.Func&lt;string, string, bool&gt;;

public class EurovisionNotificationLambda
    : PolymorphicEnum&lt;EurovisionNotificationLambda&gt;
{
    private static Policy Policy(Policy f) { return f; }

    public static EurovisionNotificationLambda ALWAYS =
        Register(data: Policy((eventCity, userCity) =&gt; true));

    public static EurovisionNotificationLambda ONLY_IN_MY_CITY =
        Register(data: Policy((eventCity, userCity) =&gt;
            eventCity.Equals(
                userCity,
                StringComparison.InvariantCultureIgnoreCase)));

    public static EurovisionNotificationLambda NEVER =
        Register(data: Policy((eventCity, userCity) =&gt; false));

    public bool MustNotify(String eventCity, String userCity)
    {
        return ((Policy)this.Data).Invoke(eventCity, userCity);
    }
}</pre>

<p style="text-align: justify;">In this implementation, each enum value owns its notification policy, in the form of a types delegate, which is declared using a lambda expression.</p>

<h3 style="text-align: justify;">Enum-non-optimized collections</h3>

<p style="text-align: justify;">Ok, I haven’t gone so far as implementing optimized collections that take advantage of the integer nature of enums ! So not all the arguments listed by @cyriux apply to my C# implementation… But I still get polymorphism, the correct Equals, GetHashCode, ToString, string or integer serialization, and singleton-ness for free, with very little code ! Feel free to experiment with it if you want, the whole project will be available on my GitHub very soon.</p>

<p style="text-align: justify;"><a title="Polymorphic enums in C#, generic incrementation" href="http://www.arolla.fr/blog/2012/10/polymorphic-enums-in-c-generic-incrementation/" target="_blank">In a next post, I’m going to talk about some points of interest in my PolymorphicEnum implementation.</a></p>
