---
linkTitle: Oredictionary
---

<article class="docs-entry">
<h1 id="oredictionary">OreDictionary<a class="headerlink" href="#oredictionary" title="Permanent link"> </a></h1>
<p>The OreDictionary exists primarily for inter-mod compatibility.</p>
<p>Items that are registered to the OreDictionary become interchangeable with other items that have the same OreDictionary name. This allows recipes to use either item to produce the same result.</p>
<p>Despite its name, the OreDictionary is used for much more than ores. Any item that is similar to another item (for example, dyes) can be registered to and used with the OreDictionary.</p>
<h2 id="oredictionary-name-convention">OreDictionary Name Convention<a class="headerlink" href="#oredictionary-name-convention" title="Permanent link"> </a></h2>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Because OreDictionary names are meant to be shared between items from different mods, they should be fairly general. Use a name that other mods are likely to use.</p>
</div>
<p>Forge does not require names to be in any particular format, but the following has become a popular standard for OreDictionary names:</p>
<p>The entire OreDictionary name typically uses camelCase (compound words that begin with a lowercase letter, where each successive word begins with a capital letter) and avoids spaces or underscores.</p>
<p>The first word in the OreDictionary name indicates the type of item. For unique items (such as <code>record</code>, <code>dirt</code>, <code>egg</code>, and <code>vine</code>), one word is specific enough.</p>
<p>The last part of the name indicates the material of the item. This differentiates between <code>ingotIron</code> and <code>ingotGold</code>, for example.</p>
<p>When two words are not specific enough, a third word can be added. For instance, granite is registered as <code>blockGranite</code> while polished granite is registered as <code>blockGranitePolished</code>.</p>
<p>See <a href="#common-oredictionary-names">Common OreDictionary Names</a> for a list of commonly used prefixes and suffixes.</p>
<h2 id="wildcard_value">WILDCARD_VALUE<a class="headerlink" href="#wildcard_value" title="Permanent link"> </a></h2>
<p>This value is used to indicate that the metadata of an <code>ItemStack</code> is not important. See <a href="#using-oredictionary-in-crafting-recipes">below</a> for an example of its use.</p>
<h2 id="using-oredictionary-in-crafting-recipes">Using OreDictionary in Crafting Recipes<a class="headerlink" href="#using-oredictionary-in-crafting-recipes" title="Permanent link"> </a></h2>
<p>Recipes that use the OreDictionary are created and registered in much the same way as regular crafting recipes. The main difference is the use of an OreDictionary name instead of a specific <code>Item</code> or <code>ItemStack</code>.</p>
<p>To make a recipe that can use OreDictionary entries, create an ingredient with <code>type</code> of <code>forge:ore_dict</code> in your recipe json, and specify the ore dictionary entry name in <code>ore</code> field:</p>
<pre class="highlight"><code class="language-json">{
    "type": "forge:ore_dict",
    "ore": "ingotIron"
}</code></pre>

<p>More information about recipe json may be found <a href="../recipes/index.htm">here</a>.</p>
<p>Another use of the OreDictionary in crafting is the <a href="#wildcard_value">WILDCARD_VALUE</a>. Use by passing <code>32767</code> into <code>data</code> field of a regular item ingredient:</p>
<pre class="highlight"><code class="language-json">{
    "item": "minecraft:wool",
    "data": 32767
}</code></pre>

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The constant <code>OreDictionary.WILDCARD_VALUE</code> (<code>32767</code>) should only be used for the recipe input. Using <code>WILDCARD_VALUE</code> in the recipe output will only hardcode the damage of the output <code>ItemStack</code>.</p>
</div>
<h2 id="registering-items-to-the-oredictionary">Registering Items to the OreDictionary<a class="headerlink" href="#registering-items-to-the-oredictionary" title="Permanent link"> </a></h2>
<p>Add entries to the OreDictionary during <a href="../../concepts/registries/index.htm#registering-things">registry events</a>, after registering your items.</p>
<p>Simply call <code>OreDictionary.registerOre(ItemStack stack, String name)</code> with an <code>ItemStack</code> containing your item or block and its metadata value to register it to the OreDictionary.</p>
<p>You can also call one of the overloads of <code>OreDictionary.registerOre</code> that take a <code>Block</code> or <code>Item</code> to avoid creating the <code>ItemStack</code> yourself.</p>
<p>See <a href="#oredictionary-name-convention">OreDictionary Name Convention</a> for tips on formatting the OreDictionary name of the <code>ItemStack</code>.</p>
<h2 id="common-oredictionary-names">Common OreDictionary Names<a class="headerlink" href="#common-oredictionary-names" title="Permanent link"> </a></h2>
<p>All OreDictionary names for Minecraft items and blocks can be found in <code>net.minecraftforge.oredict.OreDictionary</code>. A full list will not be included here.</p>
<p>Common prefixes already used in the OreDictionary include <code>ore</code>, <code>ingot</code>, <code>nugget</code>, <code>dust</code>, <code>gem</code>, <code>dye</code>, <code>block</code>, <code>stone</code>, <code>crop</code>, <code>slab</code>, <code>stair</code>, and <code>pane</code>.</p>
<p>Common prefixes for modded items include <code>gear</code>, <code>rod</code>, <code>stick</code>, <code>plate</code>, <code>dustTiny</code>, and <code>cover</code>.</p>
<p>Common suffixes in the OreDictionary include <code>Wood</code>, <code>Glass</code>, <code>Iron</code>, <code>Gold</code>, <code>Leaves</code>, and <code>Brick</code>.</p>
<p>Common suffixes for modded items include the names of metals (<code>Copper</code>, <code>Aluminum</code>, <code>Lead</code>, <code>Steel</code>, etc.) and other modded materials.</p>
</article>