---
linkTitle: Blocks
---

<article class="docs-entry">
<h1 id="blocks">Blocks<a class="headerlink" href="#blocks" title="Permanent link"> </a></h1>
<p>Blocks are, obviously, essential to the Minecraft world. They make up all of the terrain, structures, and machines. Chances are if you are interested in making a mod, then you will want to add some blocks. This page will guide you through the creation of blocks, and some of the things you can do with them.</p>
<h2 id="creating-a-block">Creating a Block<a class="headerlink" href="#creating-a-block" title="Permanent link"> </a></h2>
<h3 id="basic-blocks">Basic Blocks<a class="headerlink" href="#basic-blocks" title="Permanent link"> </a></h3>
<p>For simple blocks, which need no special functionality (think cobblestone, wood planks, etc.), a custom class is not necessary. By simply instantiating the <code>Block</code> class and calling some of the many setters, one can create many different types of blocks. For instance:</p>
<ul>
<li><code>setHardness</code> - Controls the time it takes to break the block. It is an arbitrary value. For reference, stone has a hardness of 1.5, and dirt 0.5. If the block should be unbreakable, a convenience method <code>setBlockUnbreakable</code> is provided.</li>
<li><code>setResistance</code> - Controls the explosion resistance of the block. This is separate from hardness, but <code>setHardness</code> will also set the resistance to 5 times the hardness value, if the resistance is any lower than this value.</li>
<li><code>setSoundType</code> - Controls the sound the block makes when it is punched, broken, or placed. Requires a <code>SoundType</code> argument, see the <a href="#">sounds</a> page for more details.</li>
<li><code>setLightLevel</code> - Controls the light emission of the block. <strong>Note:</strong> This method takes a value from zero to one, not zero to fifteen. To calculate this value, take the light level you wish your block to emit and divide by 16. For instance a block which emits level 5 light should pass <code>5 / 16f</code> to this method.</li>
<li><code>setLightOpacity</code> - Controls the amount light passing through this block will be dimmed. Unlike <code>setLightLevel</code> this value is on a scale from zero to 15. For example, setting this to <code>3</code> will lower light by 3 levels every time it passes through this type of block.</li>
<li><code>setUnlocalizedName</code> - Mostly self explanatory, sets the unlocalized name of the block. This name will be prepended with &ldquo;tile.&rdquo; and appended with &ldquo;.name&rdquo; for localization purposes. For instance <code>setUnlocalizedName("foo")</code> will cause the block&rsquo;s actual localization key to be &ldquo;tile.foo.name&rdquo;. For more advanced localization control, a custom Item will be needed. We&rsquo;ll get into this more later.</li>
<li><code>setCreativeTab</code> - Controls which creative tab this block will fall under. This must be called if the block should be shown in the creative menu. Tab options can be found in the <code>CreativeTabs</code> class.</li>
</ul>
<p>All these methods are <em>chainable</em> which means you can call them in series. See <code>Block#registerBlocks</code> for examples of this.</p>
<h3 id="advanced-blocks">Advanced Blocks<a class="headerlink" href="#advanced-blocks" title="Permanent link"> </a></h3>
<p>Of course, the above only allows for extremely basic blocks. If you want to add functionality, like player interaction, a custom class is required. However, the <code>Block</code> class has many methods and unfortunately not every single one can be documented here. See the rest of the pages in this section for things you can do with blocks.</p>
<h2 id="registering-a-block">Registering a Block<a class="headerlink" href="#registering-a-block" title="Permanent link"> </a></h2>
<p>Blocks must be <a href="#">registered</a> to function.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>A block in the world and a &ldquo;block&rdquo; in an inventory are very different things. A block in the world is represented by an <code>IBlockState</code>, and its behavior defined by an instance of <code>Block</code>. Meanwhile, an item in an inventory is an <code>ItemStack</code>, controlled by an <code>Item</code>. As a bridge between the different worlds of <code>Block</code> and <code>Item</code>, there exists the class <code>ItemBlock</code>. <code>ItemBlock</code> is a subclass of <code>Item</code> that has a field <code>block</code> that holds a reference to the <code>Block</code> it represents. <code>ItemBlock</code> defines some of the behavior of a &ldquo;block&rdquo; as an item, like how a right click places the block. It&rsquo;s possible to have a <code>Block</code> without an <code>ItemBlock</code>. (E.g. <code>minecraft:water</code> exists a block, but not an item. It is therefore impossible to hold it in an inventory as one.)</p>
<p>When a block is registered, <em>only</em> a block is registered. The block does not automatically have an <code>ItemBlock</code>. To create a basic <code>ItemBlock</code> for a block, one should use <code>new ItemBlock(block).setRegistryName(block.getRegistryName())</code>. The unlocalized name is the same as the block&rsquo;s. Custom subclasses of <code>ItemBlock</code> may be used as well. Once an <code>ItemBlock</code> has been registered for a block, <code>Item.getItemFromBlock</code> can be used to retrieve it. <code>Item.getItemFromBlock</code> will return <code>null</code> if there is no <code>ItemBlock</code> for the <code>Block</code>, so if you are not certain that there is an <code>ItemBlock</code> for the <code>Block</code> you are using, check for <code>null</code>.</p>
</div>
<h2 id="further-reading">Further Reading<a class="headerlink" href="#further-reading" title="Permanent link"> </a></h2>
<p>For information about block properties, such as those used for vanilla blocks like wood types, fences, walls, and many more, see the section on <a href="#">blockstates</a>.</p>
</article>