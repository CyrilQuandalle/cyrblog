---
layout: post
title: Polymorphic enums in C#, generic incrementation
date: 2012-10-09 11:41
author: pierre irrmann
comments: true
categories: [C#, enum, Programmation, Puzzle]
---
<p style="text-align: justify;"><a title="Polymorphic enums in C# ?" href="http://www.arolla.fr/blog/2012/09/polymorphic-enums-in-c/" target="_blank">Consultez ici l'article précédent</a></p>
<p style="text-align: justify;">During the implementation of my polymorphic enum, I have chosen to allow several types for the underlying “ordinal” value, as it is also possible for standard C# enums…</p>
<p align="justify">From the <a href="http://msdn.microsoft.com/en-us/library/exx3b86w.aspx" target="_blank">MSDN documentation</a>, the allowed underlying types for enum types are the following (although other value types such as char are eligible, their use is not recommended) :</p>

<table>
<tbody>
<tr>
<th>Type</th>
<th>Size</th>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/d86he86x.aspx">sbyte</a></td>
<td>Signed 8-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/5bdb6693.aspx">byte</a></td>
<td>Unsigned 8-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/ybs77ex4.aspx">short</a></td>
<td>Signed 16-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/cbf1574z.aspx">ushort</a></td>
<td>Unsigned 16-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/5kzh1b5w.aspx">int</a></td>
<td>Signed 32-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/x0sksh43.aspx">uint</a></td>
<td>Unsigned 32-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/ctetwysk.aspx">long</a></td>
<td>Signed 64-bit integer</td>
</tr>
<tr>
<td><a href="http://msdn.microsoft.com/en-us/library/t98873t4.aspx">ulong</a></td>
<td>Unsigned 64-bit integer</td>
</tr>
</tbody>
</table>
<p align="justify">In order to enable the ordinal parameter to be optional in the registration of enum values, I had to find a way to compute an automatic value. The rule I wanted to apply was very simple : if there is no value already register, use zero, else use the maximum value already registered, plus one. But how do you write such code without checking the type in an ugly fashion ?</p>
<p align="justify">Generic constraints are somehow limited : I can force the ordinal type to be a value type by using a “where T : struct” constraints, but there is no such thing as a “IPrimitiveNumericValue” interface !</p>
Here is where I got to, using generics and lambdas…
<div>
<div id="highlighter_10533">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public static class Incrementer</code></div>
<div><code>{</code></div>
<div><code>    </code><code>private static Func&lt;T, T&gt; GetFunc&lt;T&gt;(Func&lt;T, T&gt; f)</code></div>
<div><code>    </code><code>{</code></div>
<div><code>        </code><code>return f;</code></div>
<div><code>    </code><code>}</code></div>
<div><code>    </code><code>[...]</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="justify">I first define static class and a private static helper function that take a Func&lt;T, T&gt; and returns… the unmodified argument. This is just a hint for the compiler, because I’m going to use lambdas and as you might know, a lambda can either be converted to a delegate (code) or to an expression tree representing the lambda (data). Without any indication, the compiler can’t infer the expected type, so we have to help him…</p>
Then, the class defines a static field and initialises it in its static constructor :
<div>
<div id="highlighter_519614">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>private static Dictionary&lt;Type, object&gt; incrementers;</code></div>
<div><code>static Incrementer()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>incrementers = new Dictionary&lt;Type, object&gt;();</code></div>
<div><code>    </code><code>incrementers.Add(typeof(sbyte), GetFunc&lt;sbyte&gt;(i =&gt; (sbyte)(i + 1)));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(byte), GetFunc&lt;byte&gt;(i =&gt; (byte)(i + 1)));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(short), GetFunc&lt;short&gt;(i =&gt; (short)(i + 1)));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(ushort), GetFunc&lt;ushort&gt;(i =&gt; (ushort)(i + 1)));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(int), GetFunc&lt;int&gt;(i =&gt; i + 1));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(uint), GetFunc&lt;uint&gt;(i =&gt; i + 1));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(long), GetFunc&lt;long&gt;(i =&gt; i + 1));</code></div>
<div><code>    </code><code>incrementers.Add(typeof(ulong), GetFunc&lt;ulong&gt;(i =&gt; i + 1));</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p align="justify">At this point, all the required incrementing functions are expressed as lambdas and registered in a dictionary.</p>
The only method left to write is the PlusOne method :
<div>
<div id="highlighter_152948">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public static T PlusOne&lt;T&gt;(this T value)</code></div>
<div><code>    </code><code>where T : struct</code></div>
<div><code>{</code></div>
<div><code>    </code><code>object incrementer;</code></div>
<div><code>    </code><code>if (!incrementers.TryGetValue(typeof(T), out incrementer))</code></div>
<div><code>        </code><code>throw new NotSupportedException(</code></div>
<div><code>            </code><code>"This type is not supported.");</code></div>
<div><code>    </code><code>return ((Func&lt;T, T&gt;)incrementer).Invoke(value);</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Type checking and dictionary lookup. That’s it !
