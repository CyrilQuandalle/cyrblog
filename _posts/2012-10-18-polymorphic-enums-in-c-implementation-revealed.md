---
layout: post
title: Polymorphic enums in C#, implementation revealed
date: 2012-10-18 10:40
author: pierre irrmann
comments: true
categories: [C#, enum, polymorphism, Programmation, Puzzle]
---
<p style="text-align: justify;">(<a title="Polymorphic enums in C#, generic incrementation" href="http://www.arolla.fr/blog/2012/10/polymorphic-enums-in-c-generic-incrementation/" target="_blank">pour consulter l'article précédent: Polymorhic enums in C#, generic incrementation</a>)</p>

<div>
<h3 style="text-align: justify;">How can we implement a polymorphic enum to mimic the Java ones ?</h3>
<p style="text-align: justify;">In a previous post, as an answer to @cyriux’s one, I showed how we could try to use a polymorphic enum in C# in order to mimic the Java ones. I have ported to C# the Java samples, using a base class called PolymorphicEnum. In this post, I’m going to give details about the implementation of this class.</p>
<p style="text-align: justify;">The very first objectives are :</p>

<ol style="text-align: justify;">
	<li>To create an enum that derives from a base PolymorphicEnum.</li>
	<li>This derived enum must be able to define a set of values.</li>
</ol>
<h3 style="text-align: justify;">Getting started</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-1.png" alt="" /></p>
<p style="text-align: justify;">What I want to be able to write is something like :</p>

<div style="text-align: justify;">
<div id="highlighter_400440">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class SomeEnum : PolymorphicEnum</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FirstValue;</code></div>
<div><code>    </code><code>public static SomeEnum SecondValue;</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">As I can’t change the compiler behaviour, the two values declared in the previous sample must be instantiated somehow. The declaration then becomes :</p>

<div style="text-align: justify;">
<div id="highlighter_169145">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class SomeEnum : PolymorphicEnum</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FirstValue = Register();</code></div>
<div><code>    </code><code>public static SomeEnum SecondValue = Register();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">In order to be able to write such code, here is what I can do :</p>

<ol style="text-align: justify;">
	<li>First, the class I want to define is only meant to be derived from, so it must be abstract. Therefore, it can only be a class, and not a struct. So I’ll have to override the Equals and GetHashCode methods in order to get a value comparison behaviour for two different instances of enums. But if the singleton-ness is implemented properly… I should never have two instances !</li>
	<li>The class will have a generic Ordinal read-only property, where T is a value type. If not provided, this type will be an int by default.</li>
	<li>In order to instantiate enum values, the base class will have to expose a static protected method “Register”.</li>
</ol>
<p style="text-align: justify;">This leads us to this basic definition :</p>

<div style="text-align: justify;">
<div id="highlighter_995399">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public abstract class PolymorphicEnum&lt;T&gt;</code></div>
<div><code>    </code><code>where T : struct, IComparable&lt;T&gt;, IConvertible</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public T Ordinal { get; private set; }</code></div>
<div><code>    </code><code>public string Name { get; private set; }</code></div>
<div><code>    </code><code>protected PolymorphicEnum()</code></div>
<div><code>    </code><code>{</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>public override string ToString()</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return this.Name;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">And in order to get an int as the default underlying type, we define a derived class :</p>

<div style="text-align: justify;">
<div id="highlighter_285658">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public abstract class PolymorphicEnum : PolymorphicEnum&lt;int&gt;</code></div>
<div><code>{</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h3 style="text-align: justify;">Adding registration</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-2.png" alt="" /></p>
<p style="text-align: justify;">Ok, this has been quite simple. Now, we want the base class to allow its children to register instances. This means the base class will be responsible for instantiating, registering and tracking these instances. In order for the base class to instantiate the derived classes, we need to know their type in the method. To to this, we introduce a generic type parameter. We also want this method to accept an optional “ordinal” parameter, to handle the case where the ordinal values are defined by user-code. What we need is a method of the form :</p>

<div style="text-align: justify;">
<div id="highlighter_756792">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnumInstance Register&lt;TEnumInstance&gt;(</code></div>
<div><code>    </code><code>Nullable&lt;T&gt; ordinal = null)</code></div>
<div><code>where TEnumInstance : new()</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Notice the “new()” generic constraint on the type parameter : the given type has to have a parameter-less constructor, which we’ll use to instantiate it. But as we go towards the polymorphic behaviour, the enum class that we’ll have to instantiate can be a sub-class from the actual enum type ! For instance, if we take the first sample from the previous post, we have the following inheritance :</p>
<p style="text-align: justify;" align="center"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/gesture-classes.png" alt="" /></p>
<p style="text-align: justify;">Because we do not want to give both of these type arguments on each Register call, the TEnum type argument moves up to the class level, and the class definition and Register method signature shift the following way:</p>

<div style="text-align: justify;">
<table>
<tbody>
<tr>
<td>
<div>
<div id="highlighter_599260">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public abstract class PolymorphicEnum&lt;T&gt;</code></div>
<div><code>    </code><code>where T : struct, IComparable&lt;T&gt;, IConvertible </code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></td>
<td>=&gt;</td>
<td>
<div>
<div id="highlighter_148014">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public abstract class PolymorphicEnum&lt;T, TEnum&gt;</code></div>
<div><code>where T : struct, IComparable&lt;T&gt;, IConvertible</code></div>
<div><code>where TEnum : PolymorphicEnum&lt;T, TEnum&gt;, new()</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></td>
</tr>
<tr>
<td>
<div>
<div id="highlighter_992172">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnumInstance Register&lt;TEnumInstance&gt;(</code></div>
<div><code>    </code><code>Nullable&lt;T&gt; ordinal = null)</code></div>
<div><code>where TEnumInstance : new()</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></td>
<td>=&gt;</td>
<td>
<div>
<div id="highlighter_899528">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnum Register&lt;TEnumInstance&gt;(</code></div>
<div><code>    </code><code>Nullable&lt;T&gt; ordinal = null)</code></div>
<div><code>where TEnumInstance : TEnum, new()</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></td>
</tr>
</tbody>
</table>
</div>
<p style="text-align: justify;">The registration of the enum values is performed in a static dictionary, so here is at last the register method :</p>

<div style="text-align: justify;">
<div id="highlighter_724761">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnum Register&lt;TEnumInstance&gt;(</code></div>
<div><code>        </code><code>Nullable&lt;T&gt; ordinal = null)</code></div>
<div><code>    </code><code>where TEnumInstance : TEnum, new()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!ordinal.HasValue)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>ordinal = registeredInstances.Any()</code></div>
<div><code>            </code><code>? registeredInstances.Keys.Max().PlusOne()</code></div>
<div><code>            </code><code>: default(T);</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>TEnum instance = new TEnumInstance();</code></div>
<div><code>    </code><code>instance.Ordinal = ordinal.Value;</code></div>
<div><code>    </code><code>registeredInstances.Add(ordinal.Value, instance);</code></div>
<div><code>    </code><code>return instance;</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Comment : how to implement a “PlusOne” extension method, that could increment any of the underlying types supported by an enum, has been a puzzle of its own… <a href="http://www.pirrmann.net/polymorphic-enums-in-c-sharp-generic-incrementation/" target="_blank">See this post for my solution !</a></p>

<h3 style="text-align: justify;">Simplifying registration</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-3.png" alt="" /></p>
<p style="text-align: justify;">From now on we can register enum value the following way :</p>

<div style="text-align: justify;">
<div id="highlighter_667113">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class SomeEnum : PolymorphicEnum&lt;SomeEnum&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FirstValue = Register&lt;SomeEnum&gt;();</code></div>
<div><code>    </code><code>public static SomeEnum SecondValue = Register&lt;SomeEnum&gt;();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Wait !? Why must we provide the type to the Register method, if that is the same as the generic argument of the class ? Let’s move forward and define an additional Register method :</p>

<div style="text-align: justify;">
<div id="highlighter_316467">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnum Register(Nullable&lt;T&gt; ordinal = null)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return Register&lt;TEnum&gt;(ordinal);</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">And the registration gets simpler again :</p>

<div style="text-align: justify;">
<div id="highlighter_748696">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class SomeEnum : PolymorphicEnum&lt;SomeEnum&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FirstValue = Register();</code></div>
<div><code>    </code><code>public static SomeEnum SecondValue = Register();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h3 style="text-align: justify;">Finding the names</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-4.png" alt="" /></p>
<p style="text-align: justify;">So now we can register enum values, optionally specifying the ordinal value… but we’re not done yet ! I mean, aren’t we supposed to be able to convert enums from and to strings ? From the previous steps, we have a Name property with a private setter, and an overridden ToString() method. But the Name property is never set ! In my first implementations, providing the Register method with the string to associate with the value was mandatory. I was not satisfied with this solution, and I came up with the following replacement solution :</p>

<ol style="text-align: justify;">
	<li>I added a static field to the class :
<div>
<div id="highlighter_249407">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>private static bool namesInitialized = false;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
	<li>I replaced the Name property implementation with this new one :
<div>
<div id="highlighter_726294">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>private string name = null;</code></div>
<div><code>private string Name</code></div>
<div><code>{</code></div>
<div><code>    </code><code>get</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>EnsureNamesInitialized();</code></div>
<div><code>        </code><code>return this.name;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>set</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>this.name = value;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
	<li>I implemented the EnsureNamesInitialized method :
<div>
<div id="highlighter_329367">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected void EnsureNamesInitialized()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!namesInitialized)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>MemberInfo[] enumMembers = typeof(TEnum).GetMembers(</code></div>
<div><code>                </code><code>BindingFlags.Public</code></div>
<div><code>                </code><code>| BindingFlags.Static</code></div>
<div><code>                </code><code>| BindingFlags.GetField);</code></div>
<div><code>        </code><code>foreach (FieldInfo enumMember in enumMembers)</code></div>
<div><code>        </code><code>{</code></div>
<div><code>            </code><code>TEnum enumValue =</code></div>
<div><code>                </code><code>enumMember.GetValue(null) as TEnum;</code></div>
<div><code>            </code><code>if (enumValue != null)</code></div>
<div><code>                </code><code>enumValue.Name = enumMember.Name;</code></div>
<div><code>        </code><code>}</code></div>
<div><code>        </code><code>namesInitialized = true;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
	<li>And finally I added this line at the end or the register method, just before the “return instance” statement :
<div>
<div id="highlighter_5691">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>namesInitialized = false;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div></li>
</ol>
<p style="text-align: justify;">This mechanism ensures that the enum names are all computed using reflection. It assigns each enum value the name of the public static member to which it has been assigned (given the member has already been assigned and is of the correct type).</p>

<h3 style="text-align: justify;">Serializing and deserializing</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-5.png" alt="" /></p>
<p style="text-align: justify;">Nice ! We’ve made progress, really. Now we want to be able to serialize and deserialize the values from and to strings, as well as from the underlying primitive type.</p>
<p style="text-align: justify;">Serializing to a string has been implemented from the start by the ToString() method. Deserializing will imply 4 methods signatures :</p>

<ul style="text-align: justify;">
	<li>bool TryParse(string value, out TEnum result)</li>
	<li>bool TryParse(string value, bool ignoreCase, out TEnum result)</li>
	<li>TEnum Parse(string value)</li>
	<li>TEnum Parse(string value, bool ignoreCase)</li>
</ul>
<p style="text-align: justify;">In the end the implementation is the following :</p>

<div style="text-align: justify;">
<div id="highlighter_209184">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public static bool TryParse(string value,</code></div>
<div><code>                            </code><code>bool ignoreCase,</code></div>
<div><code>                            </code><code>out TEnum result)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>TEnum[] instances = registeredInstances</code></div>
<div><code>        </code><code>.Values</code></div>
<div><code>        </code><code>.Where(</code></div>
<div><code>            </code><code>e =&gt; e.Name.Equals(</code></div>
<div><code>                </code><code>value,</code></div>
<div><code>                </code><code>ignoreCase</code></div>
<div><code>                    </code><code>? StringComparison.InvariantCultureIgnoreCase</code></div>
<div><code>                    </code><code>: StringComparison.InvariantCulture))</code></div>
<div><code>        </code><code>.ToArray();</code></div>
<div><code>    </code><code>if (instances.Length == 1)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>result = instances[0];</code></div>
<div><code>        </code><code>return true;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>else</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>result = default(TEnum);</code></div>
<div><code>        </code><code>return false;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">The 3 other signatures simply rely on the previous method.</p>
<p style="text-align: justify;">Concerning the other type of conversion (i.e. from and to the underlying primitive type), to stay as close as possible as how classical enums work, I have defined the following operator :</p>

<div style="text-align: justify;">
<div id="highlighter_595022">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public static implicit operator T(PolymorphicEnum&lt;T, TEnum&gt; x)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return x.Ordinal;</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">And the second one :</p>

<div style="text-align: justify;">
<div id="highlighter_844862">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public static explicit operator PolymorphicEnum&lt;T, TEnum&gt;(T x)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>TEnum enumInstance;</code></div>
<div><code>    </code><code>if (!registeredInstances.TryGetValue(x, out enumInstance))</code></div>
<div><code>        </code><code>throw new ArgumentException(</code></div>
<div><code>            </code><code>string.Format("Enum value {0} not found", x, "x"));</code></div>
<div><code>    </code><code>return enumInstance;</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<h3 style="text-align: justify;">Embedding data</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-6.png" alt="" /></p>
<p style="text-align: justify;">Now we’re going to add an extra requirement : we want to embed data in the enum values. In order to store this values, I’ll be as open as I can and add an extra property typed as “object” :</p>

<div style="text-align: justify;">
<div id="highlighter_591639">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected object Data { get; private set; }</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">I’ll also add an extra optional parameter in the two Register methods, that become :</p>

<div style="text-align: justify;">
<div id="highlighter_207522">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnum Register(</code></div>
<div><code>    </code><code>Nullable&lt;T&gt; ordinal = null,</code></div>
<div><code>    </code><code>object data = null)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return Register&lt;TEnum&gt;(ordinal, data);</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">and :</p>

<div style="text-align: justify;">
<div id="highlighter_934526">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>protected static TEnum Register&lt;TEnumInstance&gt;(</code></div>
<div><code>    </code><code>Nullable&lt;T&gt; ordinal = null,</code></div>
<div><code>    </code><code>object data = null)</code></div>
<div><code>where TEnumInstance : TEnum, new()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!ordinal.HasValue)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>ordinal = registeredInstances.Any()</code></div>
<div><code>            </code><code>? registeredInstances.Keys.Max().PlusOne()</code></div>
<div><code>            </code><code>: default(T);</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>TEnum instance = new TEnumInstance();</code></div>
<div><code>    </code><code>instance.Ordinal = ordinal.Value;</code></div>
<div><code>    </code><code>instance.Data = data;</code></div>
<div><code>    </code><code>registeredInstances.Add(ordinal.Value, instance);</code></div>
<div><code>    </code><code>namesInitialized = false;</code></div>
<div><code>    </code><code>return instance;</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">As you can see, not much as changed, but that is all there is to do ! A single point to mention is that nothing prevents the derived classes to change the state of the objects stored in the “Data” property. So when writing these derived classes, the developer has to be careful to enforce immutability.</p>

<h3 style="text-align: justify;">Enforcing consistency</h3>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/step-7.png" alt="" /></p>
<p style="text-align: justify;">OK, I’ll soon stop this never-ending post, but there is a last detail that I wanted to explain, that is how I take care that once the set of values of an enum has been defined, it cannot be extended from any other class.</p>
<p style="text-align: justify;">In order to register new values, you have to add new enum instances in the registeredInstances member. The way to do this is to call one of the two overloads or the Register method. If you wanted to bypass these methods, you would need to have access to the registeredInstances member, which is private in the base class, so that settles this point. But what prevents you from calling the Register methods in weird situations ? Let’s take a look at the following inheritance :</p>
<p style="text-align: justify;" align="center"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/enforce-consistency-classes.png" alt="" /></p>
<p style="text-align: justify;">Put in code, it becomes :</p>

<div style="text-align: justify;">
<div id="highlighter_934963">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class SomeEnum : PolymorphicEnum&lt;SomeEnum&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FirstValue = Register();</code></div>
<div><code>    </code><code>public static SomeEnum SecondValue = Register();</code></div>
<div><code>}</code></div>
<div><code>public class OtherEnum : PolymorphicEnum&lt;SomeEnum&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum ThirdValueAttempt = Register();</code></div>
<div><code>}</code></div>
<div><code>public class OtherChildEnum : SomeEnum</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static SomeEnum FourthValueAttempt = Register();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">The above code is valid, and compiles without any warning. The register method is a protected method, so it can be called :</p>

<ul style="text-align: justify;">
	<li>from any class deriving from the same base classe PolymorphicEnum&lt;T&gt; (with the same T type), for instance here the OtherEnum class,</li>
	<li>from any sub-class, for instance here the OtherChildEnum class.</li>
</ul>
<p style="text-align: justify;">To prevent the registration of invalid values, we check at the very beginning of the Register method that the type which called the method is the same as the type being registered. To do so, we take a look at the call stack, either at the first or the second stack frame, depending on the overload of Register that was called :</p>

<div style="text-align: justify;">
<div id="highlighter_180614">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>StackFrame frame = new StackFrame(1);</code></div>
<div><code>if (frame.GetMethod().Name == "Register")</code></div>
<div><code>    </code><code>frame = new StackFrame(2);</code></div>
<div><code>MethodBase enumConstructor = frame.GetMethod();</code></div>
<div><code>if (enumConstructor.DeclaringType != typeof(TEnum))</code></div>
<div><code>    </code><code>throw new EnumInitializationException(</code></div>
<div><code>        </code><code>"Enum members cannot be registered from other enums.");</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">This prevents the user code from registering undesired values. But there is still an edge case to be handled, pointed to me be @cyriux…</p>
<p style="text-align: justify;">Let me show you what’s wrong… Remember the very first sample about the gestures ? Don’t look back ! Here is the code again :</p>

<div style="text-align: justify;">
<div id="highlighter_373831">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>/** PolymorphicEnum have behavior! **/</code></div>
<div><code>public class Gesture : PolymorphicEnum&lt;Gesture&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static Gesture ROCK = Register&lt;RockGesture&gt;();</code></div>
<div><code>    </code><code>public static Gesture PAPER = Register();</code></div>
<div><code>    </code><code>public static Gesture SCISSORS = Register();</code></div>
<div><code>    </code><code>// we can implement with the integer representation</code></div>
<div><code>    </code><code>public virtual bool Beats(Gesture other)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return this.Ordinal - other.Ordinal == 1;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>private class RockGesture : Gesture</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>public override bool Beats(Gesture other)</code></div>
<div><code>        </code><code>{</code></div>
<div><code>            </code><code>return other == SCISSORS;</code></div>
<div><code>        </code><code>}</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">The problem with this implementation is maybe not that obvious, but what if I write the following code ?</p>

<div style="text-align: justify;">
<div id="highlighter_733898">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class Godzilla : Gesture</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static Godzilla Instance = new Godzilla();</code></div>
<div><code>    </code><code>public override bool Beats(Gesture other)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return true;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Yes, this is valid code. And the following test is green !</p>

<div style="text-align: justify;">
<div id="highlighter_563952">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>[TestMethod]</code></div>
<div><code>public void godzilla_beats_all()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>Assert.IsTrue(Godzilla.Instance.Beats(Gesture.ROCK));</code></div>
<div><code>    </code><code>Assert.IsTrue(Godzilla.Instance.Beats(Gesture.PAPER));</code></div>
<div><code>    </code><code>Assert.IsTrue(Godzilla.Instance.Beats(Gesture.SCISSORS));</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Now imagine if the Gesture enum was part of a library… Nothing would prevent user code from providing a Godzilla instance wherever a Gesture is asked…</p>
<p style="text-align: justify;">There are several ways to secure this implementation :</p>

<ol style="text-align: justify;">
	<li>The logic could be provided as embedded data in the registration or the enum (using a lambda expression). Although this is the solution I would prefer (“composition over inheritance”, see the Manifesto for Not Only Object-Oriented Development), this would not be a real polymorphic behaviour anymore !</li>
	<li>If we want to solve this using polymorphism, this is what I came up with. I added the following two “Checked” methods and the IsRegistered property in the base class :</li>
</ol>
<div style="text-align: justify;">
<div id="highlighter_905964">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>private bool IsRegistered</code></div>
<div><code>{</code></div>
<div><code>    </code><code>get { return registeredInstances.Values.Contains(this); }</code></div>
<div><code>}</code></div>
<div><code>protected TEnum Checked(TEnum value)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!value.IsRegistered)</code></div>
<div><code>        </code><code>throw new UnregisteredEnumException(</code></div>
<div><code>            </code><code>"This enum is not registered");</code></div>
<div><code>    </code><code>return value;</code></div>
<div><code>}</code></div>
<div><code>protected void Checked(Action a)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!IsRegistered)</code></div>
<div><code>        </code><code>throw new UnregisteredEnumException(</code></div>
<div><code>            </code><code>"This enum is not registered");</code></div>
<div><code>    </code><code>a.Invoke();</code></div>
<div><code>}</code></div>
<div><code>protected TReturn Checked&lt;TReturn&gt;(Func&lt;TReturn&gt; f)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>if (!IsRegistered)</code></div>
<div><code>        </code><code>throw new UnregisteredEnumException(</code></div>
<div><code>            </code><code>"This enum is not registered");</code></div>
<div><code>    </code><code>return f.Invoke();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Using these new methods, here is how we could rewrite the previous “Godzilla” sample :</p>

<div style="text-align: justify;">
<div id="highlighter_655123">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class CheckedGesture : PolymorphicEnum&lt;CheckedGesture&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static CheckedGesture ROCK = Register&lt;RockGesture&gt;();</code></div>
<div><code>    </code><code>public static CheckedGesture PAPER = Register();</code></div>
<div><code>    </code><code>public static CheckedGesture SCISSORS = Register();</code></div>
<div><code>    </code><code>// we can implement with the integer representation</code></div>
<div><code>    </code><code>public bool Beats(CheckedGesture other)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return Checked(() =&gt; this.BeatsImpl(Checked(other)));</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>protected virtual bool BeatsImpl(CheckedGesture other)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return this.Ordinal - other.Ordinal == 1;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>private class RockGesture : CheckedGesture</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>protected override bool BeatsImpl(CheckedGesture other)</code></div>
<div><code>        </code><code>{</code></div>
<div><code>            </code><code>return other == SCISSORS;</code></div>
<div><code>        </code><code>}</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">And the method that can be overridden in the new Godzilla class in now the BeatsImpl one :</p>

<div style="text-align: justify;">
<div id="highlighter_928042">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public class CagedGodzilla : CheckedGesture</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public static CagedGodzilla Instance = new CagedGodzilla();</code></div>
<div><code>    </code><code>protected override bool BeatsImpl(CheckedGesture other)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return true;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">This now works correctly and the CagedGodzilla can’t be used in place of a CheckedGesture without throwing an UnregisteredEnumException. But this seems a little bit over-engineered, isn’t it ?</p>
<p style="text-align: justify;">All of this horrible mess is <a href="https://github.com/pirrmann/PolymorphicEnum" target="_blank">available on my GitHub</a>, along with the unit tests and the different steps of the implementation, which I’ve described in this post.</p>

</div>
