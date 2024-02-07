---
linkTitle: States
---

<article class="docs-entry">
<h1 id="block-states">Block States<a class="headerlink" href="#block-states" title="Permanent link"> </a></h1>
<p>Please read <strong>all</strong> of this guide before starting to code. Your understanding will be more comprehensive and correct than if you just picked parts out.
This guide is designed for an entry level introduction to Block States. If you know what Extended States are, you&rsquo;ll notice some simplifying assumptions I&rsquo;ve made below. They are intentional and are meant to avoid overloading beginners with information they may not immediately need. If you don&rsquo;t know what they are, no need to fear, there will be another document for them eventually.</p>
<h2 id="motivation">Motivation<a class="headerlink" href="#motivation" title="Permanent link"> </a></h2>
<p>In Minecraft 1.8 and above, direct manipulation of blocks and metadata values have been abstracted away into what is known as blockstates.
The premise of the system is to remove the usage and manipulation of raw metadata numbers, which are nondescript and carry no meaning.</p>
<p>For example, consider this switch statement for some arbitrary block that can face a direction and be on either half of the block space:</p>
<pre class="highlight"><code class="language-Java">switch(meta) {
  case 0: // it's south and on the lower half of the block
  case 1: // it's south on the upper side of the block
  case 2: // it's north and on the lower half of the block
  case 3: // it's north and on the upper half of the block
  ... etc.
}</code></pre>

<p>The numbers themselves carry no meaning whatsoever! If the comments weren&rsquo;t there we would have no idea what the meaning of each number is.</p>
<h2 id="a-new-way-of-thinking">A New Way of Thinking<a class="headerlink" href="#a-new-way-of-thinking" title="Permanent link"> </a></h2>
<p>How about, instead of having to munge around with numbers everywhere, we instead use some system that abstracts out the details of saving from the semantics of the block itself?
This is where <code>IProperty&lt;?&gt;</code> comes in. Each Block has a set of zero or more of these objects, that describe, unsurprisingly, <em>properties</em> that the block have. Examples of this include color (<code>IProperty&lt;EnumDyeColor&gt;</code>), facing (<code>IProperty&lt;EnumFacing&gt;</code>), integer and boolean values, etc. Each property can have a <em>value</em> of the type parametrized by <code>IProperty</code>. For example, for the respective example properties, we can have values <code>EnumDyeColor.WHITE</code>, <code>EnumFacing.EAST</code>, <code>1</code>, or <code>false</code>.</p>
<p>Then, following from this, we see that every unique triple (Block, set of properties, set of values for those properties) is a suitable abstracted replacement for Block and metadata. Now, instead of &ldquo;minecraft:stone_button meta 9&rdquo; we have &ldquo;minecraft:stone_button[facing=east,powered=true]&rdquo;. Guess which is more meaningful?</p>
<p>We have a very special name for these triples - they&rsquo;re called <code>IBlockState</code>&lsquo;s.</p>
<h2 id="imbuing-your-blocks-with-these-magical-properties">Imbuing your Blocks with these Magical Properties<a class="headerlink" href="#imbuing-your-blocks-with-these-magical-properties" title="Permanent link"> </a></h2>
<p>Now that I&rsquo;ve successfully convinced you that properties and values are superior to arbitrary numbers, let&rsquo;s move on to the actual how-to-do part.</p>
<p>In your Block class, create static final <code>IProperty&lt;&gt;</code> objects for every property that your Block has. Vanilla provides us several convenience implementations:</p>
<ul>
<li><code>PropertyInteger</code>: Implements <code>IProperty&lt;Integer&gt;</code>. Created by calling PropertyInteger.create(&ldquo;<name>&ldquo;, <min>, <max>);</max></min></name></li>
<li><code>PropertyBool</code>: Implements <code>IProperty&lt;Boolean&gt;</code>. Created by calling PropertyBool.create(&ldquo;<name>&ldquo;);</name></li>
<li><code>PropertyEnum&lt;E extends Enum&lt;E&gt;&gt;</code>: Implements <code>IProperty&lt;E&gt;</code>, Defines a property that can take on the values of an Enum class. Created by calling PropertyEnum.create(&ldquo;name&rdquo;, <enum_class>);<ul>
<li>You can also use only a subset of the Enum values (for example, you can use only 4 of the 16 <code>EnumDyeColor</code>&lsquo;s. Take a look at the other overloads of <code>PropertyEnum.create</code>)</li>
</ul>
</enum_class></li>
<li><code>PropertyDirection</code>: This is a convenience implementation of <code>PropertyEnum&lt;EnumFacing&gt;</code><ul>
<li>Several convenience predicates are also provided. For example, to get a property that represents the cardinal directions, you would call <code>PropertyDirection.create("&lt;name&gt;", EnumFacing.Plane.HORIZONTAL)</code>. Or to get the X directions, <code>PropertyDirection.create("&lt;name&gt;", EnumFacing.Axis.X)</code></li>
</ul>
</li>
</ul>
<p>Note that you are free to make your own <code>IProperty&lt;&gt;</code> implementations, but the means to do that are not covered in this article.
In addition, note that you can share the same <code>IProperty</code> object between different blocks if you wish. Vanilla generally has separate ones for every single block, but it is merely personal preference.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>If your mod has an API or is meant to be interacted with from other mods, it is <strong>very highly</strong> recommended that you instead place your <code>IProperty</code>&lsquo;s (and any classes used as values) in your API. That way, people can use properties and values to set your blocks in the world instead of having to suffer with arbitrary numbers like you used to.</p>
</div>
<p>After you&rsquo;ve created your <code>IProperty&lt;&gt;</code> objects, override <code>createBlockState</code> in your Block class. In that method, simply write <code>return new BlockState()</code>. Pass the <code>BlockState</code> constructor first your Block, <code>this</code>, then follow it with every <code>IProperty</code> you want to declare. Note that in 1.9 and above, the <code>BlockState</code> class has been renamed to <code>BlockStateContainer</code>, more in line with what this class actually does.</p>
<p>The object you just created is a pretty magical one - it manages the generation of all the triples above. That is, it generates all possible combinations of every value for each property (for math-oriented people, it takes the set of possible values of each property and computes the cartesian product of those sets). Thus, it generates every unique (Block, properties, values) possible - every <code>IBlockState</code> possible for the given properties.</p>
<p>If you do not set one of these <code>IBlockState</code>&lsquo;s to act as the &ldquo;default&rdquo; state for your Block, then one is chosen for you. You probably don&rsquo;t want this (it will cause weird things to happen most of the time), so at the end of your Block&rsquo;s constructor call <code>setDefaultState()</code>, passing in the <code>IBlockState</code> you want to be the default. Get the one that was chosen for you using <code>this.blockState.getBaseState()</code> then set a value for <em>every</em> property using <code>withProperty</code></p>
<p>Because <code>IBlockState</code>&lsquo;s are immutable and pregenerated, calling <code>IBlockState.withProperty(&lt;PROPERTY&gt;, &lt;NEW_VALUE&gt;)</code> will simply go to the <code>BlockState</code>/<code>BlockStateContainer</code> and request the IBlockState with the set of values you want, instead of constructing a new <code>IBlockState</code>.</p>
<p>It follows very easily from this that since basic <code>IBlockState</code>&lsquo;s are generated into a fixed set at startup, you are able and encouraged to use reference comparison (==) to check if they are equal!</p>
<h2 id="using-iblockstates">Using <code>IBlockState</code>&lsquo;s<a class="headerlink" href="#using-iblockstates" title="Permanent link"> </a></h2>
<p><code>IBlockState</code>, as we know now, is a powerful object. You can get the value of a property by calling <code>getValue(&lt;PROPERTY&gt;)</code>, passing it the <code>IProperty&lt;&gt;</code> you want to test.
If you want to get an IBlockState with a different set of values, simply call <code>withProperty(&lt;PROPERTY&gt;, &lt;NEW_VALUE&gt;)</code> as mentioned above. This will return another of the pregenerated <code>IBlockState</code>&lsquo;s with the values you requested.</p>
<p>You can get and put <code>IBlockState</code>&lsquo;s in the world using <code>setBlockState()</code> and <code>getBlockState()</code>.</p>
<h2 id="illusion-breaker">Illusion Breaker<a class="headerlink" href="#illusion-breaker" title="Permanent link"> </a></h2>
<p>Sadly, abstractions are lies at their core. We still have the responsibility of translating every <code>IBlockState</code> back into a number between 0 and 15 inclusive that will be stored in the world and vice versa for loading.</p>
<p>If you declare any <code>IProperty</code>&lsquo;s, you <strong>must</strong> override <code>getMetaFromState</code> and <code>getStateFromMeta</code></p>
<p>Here you will read the values of your properties and return an appropriate integer between 0 and 15, or the other way around; the reader is left to check examples from vanilla blocks by themselves.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>Your <code>getMetaFromState</code> and <code>getStateFromMeta</code> methods <strong>must</strong> be one to one! In other words, the same set of properties and values must map to the same meta value and back. Failing to do this, unfortunately, <strong>won&rsquo;t</strong> cause a crash. It&rsquo;ll just cause everything to behave extremely weirdly.</p>
</div>
<h2 id="actual-states">&ldquo;Actual&rdquo; States<a class="headerlink" href="#actual-states" title="Permanent link"> </a></h2>
<p>Some sharper minds might know that fences don&rsquo;t save any of their connections to meta, yet they still have properties and values in the F3 menu! What is this blasphemy?!</p>
<p>Blocks can declare properties that are not saved to metadata. These are usually used for rendering purposes, but could possibly have other useful applications.
You still declare them in <code>createBlockState</code> and set their value in <code>setDefaultState</code>. However, these properties you do <strong>not</strong> touch <strong>at all</strong> in <code>getMetaFromState</code> and <code>getStateFromMeta</code>.</p>
<p>Instead, override <code>getActualState</code> in your Block class. Here you will receive the <code>IBlockState</code> corresponding with the metadata in the world, and you return another <code>IBlockState</code> with missing information such as fence connections, redstone connections , etc. filled in using <code>withProperty</code>. You can also use this to read Tile Entity data for a value (with appropriate safety checks of course!).</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>When you read tile entity data in <code>getActualState</code> you must perform additional safety checks. By default, <code>getTileEntity</code> attempts to create the tile entity if it is not already present. However, <code>getActualState</code> and <code>getExtendedState</code> can and will be called from different threads, which can cause the world&rsquo;s tile entity list to throw a <code>ConcurrentModificationException</code> if it tries to create a missing tile entity. Therefore, you must check if the <code>IBlockAccess</code> argument is a <code>ChunkCache</code> (the object passed to alternate threads), and if so, cast and use the non-writing variant of <code>getTileEntity</code>. An example of this safety check can be found in <code>BlockFlowerPot.getActualState()</code>.</p>
</div>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Querying <code>world.getBlockState()</code> will give you the <code>IBlockState</code> representing only the metadata. Thus the returned <code>IBlockState</code> will not have data from <code>getActualState</code> filled in. If that matters to your code, make sure you call <code>getActualState</code>!</p>
</div>
<h2 id="extended-blockstates"><a href="../../models/advanced/extended-blockstates/index.htm">Extended Blockstates</a><a class="headerlink" href="#extended-blockstates" title="Permanent link"> </a></h2>
<p>Extended blockstates are a way to pass arbitrary data into <code>IBakedModel</code>s during the rendering of a block. They are mainly used in the context of rendering, and therefore are documented in the Models section.</p>
</article>