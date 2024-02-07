---
linkTitle: Overview
---

<article class="docs-entry">
<h1 id="overview">Overview<a class="headerlink" href="#overview" title="Permanent link"> </a></h1>
<p>There are two primary goals in network communication:</p>
<ol>
<li>Making sure the client view is &ldquo;in sync&rdquo; with the server view<ul>
<li>The flower at coordinates X,Y,Z just grew</li>
</ul>
</li>
<li>Giving the client a way to tell the server that something has changed about the player<ul>
<li>the player pressed a key</li>
</ul>
</li>
</ol>
<p>The most common way to accomplish these goals is to pass messages between the client and the server. These messages will
usually be structured, containing data in a particular arrangement, for easy sending and receiving.</p>
</article>