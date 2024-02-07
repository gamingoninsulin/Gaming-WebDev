---
linkTitle: Interaction
---

<article class="docs-entry">
<h1 id="block-interaction">Block Interaction<a class="headerlink" href="#block-interaction" title="Permanent link"> </a></h1>
<p>There are many different ways players (and other things) can interact with blocks, such as right clicking, left clicking, colliding, walking on, and of course mining.</p>
<p>This page will cover the basics of the most common types of interaction with blocks.</p>
<h2 id="player-right-click">Player Right Click<a class="headerlink" href="#player-right-click" title="Permanent link"> </a></h2>
<p>Since left clicking, or &ldquo;punching&rdquo;, a block does not generally result in any unique behavior, it is probably fair to say right clicking, or &ldquo;activation&rdquo;, is <em>the</em> most common method of interaction. And thankfully, it is also one of the simplest to handle.</p>
<h3 id="onblockactivated"><code>onBlockActivated</code><a class="headerlink" href="#onblockactivated" title="Permanent link"> </a></h3>
<p>This is the method that controls right click behavior, and it is a rather complicated one. Here is its full signature:</p>
<pre class="highlight"><code class="language-java">public boolean onBlockActivated(World worldIn,
                                BlockPos pos,
                                IBlockState state,
                                EntityPlayer playerIn,
                                EnumHand hand,
                                @Nullable ItemStack heldItem,
                                EnumFacing side,
                                float hitX,
                                float hitY,
                                float hitZ)</code></pre>

<p>There&rsquo;s quite a bit to discuss here.</p>
<h4 id="parameters">Parameters<a class="headerlink" href="#parameters" title="Permanent link"> </a></h4>
<p>The first few parameters are obvious, they are the current world, position, and state of the block. Next is the player that is activating the block, and the hand with which they are activating.</p>
<p>The next parameter, <code>heldItem</code>, is the <code>ItemStack</code> which the player activated the block with. Note that this parameter is <code>@Nullable</code> meaning it can be passed as null (i.e. no item was in the hand).</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>The ItemStack passed to this method is the one you should use for checking what was in the player&rsquo;s hand. Grabbing the currently held stack from the player is unreliable as it may have changed since activation.</p>
</div>
<p>The last four parameters are all related. <code>side</code> is obviously the side which was activated, however <code>hitX</code>, <code>hitY</code>, and <code>hitZ</code> are a bit less obvious. These are the coordinates of the activation on the block&rsquo;s bounds. They are on the range 0 to 1, and represent where exactly &ldquo;on&rdquo; the block the player clicked.</p>
<h4 id="return-value">Return Value<a class="headerlink" href="#return-value" title="Permanent link"> </a></h4>
<p>What is this magic boolean which must be returned? Simply put this, is whether or not the method &ldquo;did&rdquo; something. Return true if some action was performed, this will prevent further things from happening, such as item activation.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Returning <code>false</code> from this method on the client will prevent it being called on the server. It is common practice to just check <code>worldIn.isRemote</code> and return <code>true</code>, and otherwise go on to normal activation logic. Vanilla has many examples of this, such as the chest.</p>
</div>
<h3 id="uses">Uses<a class="headerlink" href="#uses" title="Permanent link"> </a></h3>
<p>The uses for activation are literally endless. However, there are some common ones which deserve their own section.</p>
<h4 id="guis">GUIs<a class="headerlink" href="#guis" title="Permanent link"> </a></h4>
<p>One of the most common things to do on block activation is opening a GUI. Many blocks in vanilla behave this way, such as chests, hoppers, furnaces, and many more. More about GUIs can be found on <a href="../GUIs">their page</a>.</p>
<h4 id="activation">Activation<a class="headerlink" href="#activation" title="Permanent link"> </a></h4>
<p>Another common use for activation is, well, activation. This can be something like &ldquo;turning on&rdquo; a block, or triggering it to perform some action. For instance, a block could light up when activated. A vanilla example would be buttons or levers.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p><code>onBlockActivated</code> is called on both the client and the server, so be sure to keep the <a href="../../concepts/sides/index.htm">sidedness</a> of your code in mind. Many things, like opening GUIs and modifying the world, should only be done on the server-side.</p>
</div>
<h2 id="player-breakdestroy">Player Break/Destroy<a class="headerlink" href="#player-breakdestroy" title="Permanent link"> </a></h2>
<p><em>Coming Soon</em></p>
<h2 id="player-highlighting">Player Highlighting<a class="headerlink" href="#player-highlighting" title="Permanent link"> </a></h2>
<p><em>Coming Soon</em></p>
<h2 id="entity-collision">Entity Collision<a class="headerlink" href="#entity-collision" title="Permanent link"> </a></h2>
<p><em>Coming Soon</em></p>
<h2 id="onblockclicked"><code>onBlockClicked</code><a class="headerlink" href="#onblockclicked" title="Permanent link"> </a></h2>
<pre class="highlight"><code class="language-java">public void onBlockClicked(World worldIn, BlockPos pos, EntityPlayer playerIn)</code></pre>

<p>Called on a block when it is clicked by a player.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This method is for when the player <em>left-clicks</em> on a block.
Don&rsquo;t get this confused with <code>onBlockActivated</code>, which is called when the player <em>right-clicks</em>.</p>
</div>
<h3 id="parameters_1">Parameters:<a class="headerlink" href="#parameters_1" title="Permanent link"> </a></h3>
<table>
<thead>
<tr>
<th align="center">Type</th>
<th align="center">Name</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><code>World</code></td>
<td align="center"><code>worldIn</code></td>
<td align="left">The world that the block was clicked in</td>
</tr>
<tr>
<td align="center"><code>BlockPos</code></td>
<td align="center"><code>pos</code></td>
<td align="left">The position of the block that was clicked</td>
</tr>
<tr>
<td align="center"><code>EntityPlayer</code></td>
<td align="center"><code>playerIn</code></td>
<td align="left">The player who did the clicking</td>
</tr>
</tbody>
</table>
<h3 id="usage-example">Usage example<a class="headerlink" href="#usage-example" title="Permanent link"> </a></h3>
<p>This method is perfect for adding custom events when a player clicks on a block.</p>
<p>By default this method does nothing.<br>
Two blocks that override this method are the <strong>Note Block</strong> and the <strong>RedstoneOre Block</strong>.</p>
<p>The Note block overrides this method so that when left-clicked, it plays a sound.<br>
The RedstoneOre block overrides method so that when left-clicked, it gives off emits faint light for a few seconds.</p>
<h2 id="onblockdestroyedbyplayer"><code>onBlockDestroyedByPlayer</code><a class="headerlink" href="#onblockdestroyedbyplayer" title="Permanent link"> </a></h2>
<pre class="highlight"><code class="language-java">public void onBlockDestroyedByPlayer(World worldIn, BlockPos pos, IBlockState state)</code></pre>

<p>Called on a block after it&rsquo;s destroyed by a player</p>
<h3 id="parameters_2">Parameters:<a class="headerlink" href="#parameters_2" title="Permanent link"> </a></h3>
<table>
<thead>
<tr>
<th align="center">Type</th>
<th align="center">Name</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><code>World</code></td>
<td align="center"><code>worldIn</code></td>
<td align="left">The world that the block was destroyed</td>
</tr>
<tr>
<td align="center"><code>BlockPos</code></td>
<td align="center"><code>pos</code></td>
<td align="left">The position of the block that was destroyed</td>
</tr>
<tr>
<td align="center"><code>IBlockState</code></td>
<td align="center"><code>state</code></td>
<td align="left">The state of the block that was destroyed</td>
</tr>
</tbody>
</table>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>The <code>pos</code> parameter may not hold the state indicated</p>
</div>
<h3 id="usage-example_1">Usage example<a class="headerlink" href="#usage-example_1" title="Permanent link"> </a></h3>
<p>This method is perfect for adding custom events as a result of a player destroying a block</p>
<p>By default this method does nothing.  </p>
<p>The <strong>TNT Block</strong> overrides this method to cause it&rsquo;s explosion when a player destroys it.<br>
This method is used by extended pistons; since an extended piston is made up of two blocks (the extended head and the base), 
the <strong>PistonMoving Block</strong> makes use of this method to destroy the base block when the PistonMoving block is destroyed. </p>
</article>