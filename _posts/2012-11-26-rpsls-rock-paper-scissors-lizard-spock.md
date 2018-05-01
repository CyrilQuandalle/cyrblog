---
layout: post
title: RPSLS — Rock Paper Scissors Lizard Spock
date: 2012-11-26 11:16
author: pierre irrmann
comments: true
categories: [Programmation]
---
<div>
<p style="text-align: justify;">Let’s start with a tweet from earlier today :</p>

<div id="twitter-widget-0" lang="fr">
<div>
<blockquote data-twt-id="271551511631376384" data-twt-intents="false" data-twt-product="tweetembed">
<div><a href="https://twitter.com/TheRealLeonardH" data-screen-name="TheRealLeonardH">  Leonard Hofstadter @<strong>TheRealLeonardH</strong> </a></div>
<div>

Anyone bored and have an android phone? <a title="https://play.google.com/store/apps/details?id=cc.spock.ui" href="https://t.co/iUYY4xYK">play.google.com/store/apps/det…</a>

</div>
<div><a href="https://twitter.com/TheRealLeonardH/statuses/271551511631376384"> 22 Nov 12 </a>
<ul>
	<li><a title="Répondre" href="https://twitter.com/intent/tweet?in_reply_to=271551511631376384"><strong>Répondre</strong></a></li>
	<li><a title="Retweeter" href="https://twitter.com/intent/retweet?tweet_id=271551511631376384"><strong>Retweeter</strong></a></li>
	<li><a title="Favori" href="https://twitter.com/intent/favorite?tweet_id=271551511631376384"><strong>Favori</strong></a></li>
</ul>
</div></blockquote>
</div>
</div>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/09/geek-gestures-rpsls.jpg" alt="" align="right" />If you follow the link, you’ll get to a Google Play application that allows you to play the (in-)famous <a href="http://en.wikipedia.org/wiki/Rock-paper-scissors-lizard-Spock" target="_blank">RPSLS (Rock-paper-scissors-lizard-Spock)</a> game. Why that tweet stroke me is that I had already played (with code !) around that game a few months ago…</p>
<p style="text-align: justify;">Although this is just a variant of the classical rock-paper-scissors game, the two additional gestures shift the number of gesture combinations to 25, as shown on this matrix:</p>
<p style="text-align: justify;"><img src="http://www.pirrmann.net/wp-content/uploads/2012/11/Normal_form_matrix_of_Rock-paper-scissors-lizard-Spock.jpg" alt="" /></p>
<p style="text-align: justify;"><span style="font-size: xx-small;">(image taken from </span><a title="http://en.wikipedia.org/wiki/File:Normal_form_matrix_of_Rock-paper-scissors-lizard-Spock.jpg" href="http://en.wikipedia.org/wiki/File:Normal_form_matrix_of_Rock-paper-scissors-lizard-Spock.jpg"><span style="font-size: xx-small;">http://en.wikipedia.org/wiki/File:Normal_form_matrix_of_Rock-paper-scissors-lizard-Spock.jpg</span></a><span style="font-size: xx-small;">)</span></p>
<p style="text-align: justify;">There could be several ways to test these different combinations, for instance using the previous matrix as a reference, but… come on…. a closed set of values, and a simple behaviour… That’s just a perfect candidate for an enum ! And the best part is that I have actually implemented this as a test two months ago (<a href="https://github.com/pirrmann/PolymorphicEnum/commit/f5e0a18fa2df12f95cfa06f31efe44aa16a67a1e" target="_blank">proof on Github !</a>).</p>
<p style="text-align: justify;">Let’s first define the possible game outcomes :</p>

<div style="text-align: justify;">
<div id="highlighter_59520">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public</code> <code>class</code> <code>GameOutcome : PolymorphicEnum&lt;GameOutcome&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GameOutcome WIN = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GameOutcome LOSE = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GameOutcome DRAW = Register();</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Then we define the Gesture class and its values :</p>

<div style="text-align: justify;">
<div id="highlighter_10295">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>public</code> <code>class</code> <code>GeekGesture : PolymorphicEnum&lt;GeekGesture&gt;</code></div>
<div><code>{</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GeekGesture ROCK = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GeekGesture PAPER = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GeekGesture SCISSORS = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GeekGesture SPOCK = Register();</code></div>
<div><code>    </code><code>public</code> <code>static</code> <code>GeekGesture LIZARD = Register();</code></div>
<div><code>    </code><code>[...]</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">The trick here is that I changed the order of the values int the enum from Rock – paper – scissors – lizard – Spock to Rock – paper – scissors – Spock – lizard. This way, each values beats its predecessor and its second successor, and loses against its successor and second predecessor…</p>
<p style="text-align: justify;">The behaviour can then be simply implemented based on the integer values of the enums :</p>

<div style="text-align: justify;">
<div id="highlighter_22424">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>// we can use the int representation and a modulo trick !</code></div>
<div><code>public</code> <code>GameOutcome PlayAgainst(GeekGesture other)</code></div>
<div><code>{</code></div>
<div><code>    </code><code>return</code> <code>this</code> <code>== other</code></div>
<div><code>        </code><code>? GameOutcome.DRAW</code></div>
<div><code>        </code><code>: (GameOutcome)(((other + 5 - </code><code>this</code><code>) % 5) % 2);</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">Now we can write the tests in a very expressive way :</p>

<div style="text-align: justify;">
<div id="highlighter_266836">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>
<div>
<div><code>[TestMethod]</code></div>
<div><code>public</code> <code>void</code> <code>gesture_against_2nd_next_is_a_win()</code></div>
<div><code>{</code></div>
<div><code>    </code><code>Assert.AreEqual(GameOutcome.WIN,</code></div>
<div><code>        </code><code>GeekGesture.ROCK.PlayAgainst(GeekGesture.SCISSORS));</code></div>
<div><code>    </code><code>Assert.AreEqual(GameOutcome.WIN,</code></div>
<div><code>        </code><code>GeekGesture.PAPER.PlayAgainst(GeekGesture.SPOCK));</code></div>
<div><code>    </code><code>Assert.AreEqual(GameOutcome.WIN,</code></div>
<div><code>        </code><code>GeekGesture.SCISSORS.PlayAgainst(GeekGesture.LIZARD));</code></div>
<div><code>    </code><code>Assert.AreEqual(GameOutcome.WIN,</code></div>
<div><code>        </code><code>GeekGesture.SPOCK.PlayAgainst(GeekGesture.ROCK));</code></div>
<div><code>    </code><code>Assert.AreEqual(GameOutcome.WIN,</code></div>
<div><code>        </code><code>GeekGesture.LIZARD.PlayAgainst(GeekGesture.PAPER));</code></div>
<div><code>}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<p style="text-align: justify;">So… here is my answer to Leonard :</p>

<div id="twitter-widget-1" lang="fr">
<div>
<blockquote data-twt-id="271551511631376384" data-twt-intents="false" data-twt-product="tweetembed">
<div><a href="https://twitter.com/TheRealLeonardH" data-screen-name="TheRealLeonardH">  Leonard Hofstadter @<strong>TheRealLeonardH</strong> </a> <a href="https://twitter.com/TheRealLeonardH/statuses/271551511631376384" rel="bookmark"> 22 Nov 12 </a></div>
<div>

Anyone bored and have an android phone? <a title="https://play.google.com/store/apps/details?id=cc.spock.ui" href="https://t.co/iUYY4xYK">play.google.com/store/apps/det…</a>

</div></blockquote>
<blockquote data-twt-id="271729506933415936" data-twt-intents="false" data-twt-product="tweetembed">
<div style="text-align: justify;"><a href="https://twitter.com/pirrmann" data-screen-name="pirrmann">  Pierre Irrmann @<strong>pirrmann</strong> </a></div>
<div style="text-align: justify;">

<a title="Leonard Hofstadter" href="https://twitter.com/TheRealLeonardH">@<strong>TheRealLeonardH</strong></a> I have and android phone, but when I’m bored I think about software, and sometimes blog about it : <a title="http://www.pirrmann.net/rpsls-rock-paper-scissors-lizard-spock/" href="http://t.co/EyihOhy1">pirrmann.net/rpsls-rock-pap…</a>

</div>
<div><a href="https://twitter.com/pirrmann/statuses/271729506933415936"> 22 Nov 12 </a>
<ul>
	<li style="text-align: justify;"><a title="Répondre" href="https://twitter.com/intent/tweet?in_reply_to=271729506933415936"><strong>Répondre</strong></a></li>
	<li style="text-align: justify;"><a title="Retweeter" href="https://twitter.com/intent/retweet?tweet_id=271729506933415936"><strong>Retweeter</strong></a></li>
	<li style="text-align: justify;"><a title="Favori" href="https://twitter.com/intent/favorite?tweet_id=271729506933415936"><strong>Favori</strong></a></li>
</ul>
</div></blockquote>
</div>
</div>
</div>
