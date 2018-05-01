---
layout: post
title: Cartesian products in LINQ (fluent syntax)
date: 2012-11-15 11:28
author: pierre irrmann
comments: true
categories: [C#, Extension Methods, LINQ, Programmation]
---
<div>
<p style="text-align: justify;">Have you ever tried to combine sequences in order to build Cartesian products in LINQ ? This is really easily achieved using the query expressions syntax, writing for instance :</p>

<div style="text-align: justify;">
<div id="highlighter_74075">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var ints = Enumerable.Range(1, 4);</code></div>
<div><code>var longs = Enumerable.Range(1, 3).Select(i =&gt; (</code><code>long</code><code>)i);</code></div>
<div><code>var products = from i </code><code>in</code> <code>ints</code></div>
<div><code>    </code><code>from l </code><code>in</code> <code>longs</code></div>
<div><code>    </code><code>select i * l;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">When I write LINQ queries, I tend to use as often as possible the fluent syntax, as it is more powerful, and allows me to use a wider range of operators. I also try not to mix both syntaxes, because I find it quite confusing. When I want to perform, inside of a more complex query, a simple cross-product as the previous one, the “fluent” equivalent syntax is the following :</p>

<div style="text-align: justify;">
<div id="highlighter_114217">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var products = ints.SelectMany(i =&gt; longs.Select(l =&gt; i * l));</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">In fact, this syntax works very well, but I don’t find it as expressive as the first one. And if you go one step further and add a third sequence to the party…</p>

<div style="text-align: justify;">
<div id="highlighter_354894">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var ints = Enumerable.Range(1, 4);</code></div>
<div><code>var longs = Enumerable.Range(1, 3).Select(i =&gt; (</code><code>long</code><code>)i);</code></div>
<div><code>var strings = </code><code>new</code><code>[] { </code><code>"a"</code><code>, </code><code>"b"</code><code>, </code><code>"c"</code> <code>};</code></div>
<div><code>var classic = from i </code><code>in</code> <code>ints</code></div>
<div><code>    </code><code>from l </code><code>in</code> <code>longs</code></div>
<div><code>    </code><code>from s </code><code>in</code> <code>strings</code></div>
<div><code>    </code><code>select (i * l).ToString() + s;</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">The “fluent” syntax gets even more confusing (at least to me) :</p>

<div style="text-align: justify;">
<div id="highlighter_312136">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var fluent = ints.SelectMany(</code></div>
<div><code>    </code><code>i =&gt; longs.SelectMany(</code></div>
<div><code>    </code><code>l =&gt; strings.Select(s =&gt; (i * l).ToString() + s)));</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">I think that what bothers me might be the ever-increasing number of unclosed parenthesis… Anyway, as usual, I played with extensions methods and here is what I came up with, using a new “Cross” method, for the 2 sequences cross-product :</p>

<div style="text-align: justify;">
<div id="highlighter_789957">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var results = ints</code></div>
<div><code>    </code><code>.Cross(longs)</code></div>
<div><code>    </code><code>.Select((i, l) =&gt; i * l);</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">And the same syntax for the 3 sequences cross product :</p>

<div style="text-align: justify;">
<div id="highlighter_327480">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>var results = ints</code></div>
<div><code>    </code><code>.Cross(longs)</code></div>
<div><code>    </code><code>.Cross(strings)</code></div>
<div><code>    </code><code>.Select((i, l, s) =&gt; (i * l).ToString() + s);</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Don’t you find it more expressive ?</p>
<p style="text-align: justify;">Finally, here are the extensions methods implementations :</p>

<div style="text-align: justify;">
<div>
<div id="highlighter_13274">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public</code> <code>static</code> <code>IEnumerable&lt;Tuple&lt;TLeft, TRight&gt;&gt; Cross&lt;TLeft, TRight&gt;(</code></div>
<div><code>    </code><code>this</code> <code>IEnumerable&lt;TLeft&gt; left, IEnumerable&lt;TRight&gt; right)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return</code> <code>left.SelectMany(l =&gt; right.Select(r =&gt; Tuple.Create(l, r)));</code></div>
<div><code>}</code></div>
<div><code>public</code> <code>static</code> <code>IEnumerable&lt;Tuple&lt;TLeft1, TLeft2, TRight&gt;&gt; Cross&lt;TLeft1, TLeft2, TRight&gt;(</code></div>
<div><code>    </code><code>this</code> <code>IEnumerable&lt;Tuple&lt;TLeft1, TLeft2&gt;&gt; left, IEnumerable&lt;TRight&gt; right)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return</code> <code>left.SelectMany(l =&gt; right.Select(r =&gt; Tuple.Create(l.Item1, l.Item2, r)));</code></div>
<div><code>}</code></div>
<div><code>public</code> <code>static</code> <code>IEnumerable&lt;TResult&gt; Select&lt;T1, T2, TResult&gt;(</code></div>
<div><code>    </code><code>this</code> <code>IEnumerable&lt;Tuple&lt;T1, T2&gt;&gt; source, Func&lt;T1, T2, TResult&gt; selector)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return</code> <code>source.Select(t =&gt; selector(t.Item1, t.Item2));</code></div>
<div><code>}</code></div>
<div><code>public</code> <code>static</code> <code>IEnumerable&lt;TResult&gt; Select&lt;T1, T2, T3, TResult&gt;(</code></div>
<div><code>    </code><code>this</code> <code>IEnumerable&lt;Tuple&lt;T1, T2, T3&gt;&gt; source, Func&lt;T1, T2, T3, TResult&gt; selector)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return</code> <code>source.Select(t =&gt; selector(t.Item1, t.Item2, t.Item3));</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
<p style="text-align: justify;">I must admit that those generic methods signatures are horrible, though…</p>

</div>
