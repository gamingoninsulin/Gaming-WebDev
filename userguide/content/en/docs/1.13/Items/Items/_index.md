---
linkTitle: Items
---

<article class="docs-entry">
<h1 id="items">Items<a class="headerlink" href="#items" title="Permanent link"> </a></h1>
<p>Along with blocks, items are a key component of most mods. While blocks make up the world around you, items are what let you change it.</p>
<h2 id="creating-an-item">Creating an Item<a class="headerlink" href="#creating-an-item" title="Permanent link"> </a></h2>
<h3 id="basic-items">Basic Items<a class="headerlink" href="#basic-items" title="Permanent link"> </a></h3>
<p>Basic items that need no special functionality (think sticks or sugar) don&rsquo;t need custom classes. You can simply instantiate <code>Item</code> and call its various setters to set some simple properties.</p>
<table>
<thead>
<tr>
<th align="center">Method</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><code>setCreativeTab</code></td>
<td align="left">Sets which creative tab this item is under. Must be called if this item is meant to be shown on the creative menu. Vanilla tabs can be found in the class <code>CreativeTabs</code>.</td>
</tr>
<tr>
<td align="center"><code>setMaxDamage</code></td>
<td align="left">Sets the maximum damage value for this item. If it&rsquo;s over <code>0</code>, 2 item properties &ldquo;damaged&rdquo; and &ldquo;damage&rdquo; are added.</td>
</tr>
<tr>
<td align="center"><code>setMaxStackSize</code></td>
<td align="left">Sets the maximum stack size.</td>
</tr>
<tr>
<td align="center"><code>setNoRepair</code></td>
<td align="left">Makes this item impossible to repair, even if it is damageable.</td>
</tr>
<tr>
<td align="center"><code>setUnlocalizedName</code></td>
<td align="left">Sets this item&rsquo;s unlocalized name, with &ldquo;item.&rdquo; prepended.</td>
</tr>
<tr>
<td align="center"><code>setHarvestLevel</code></td>
<td align="left">Adds or removes a pair of harvest class (<code>"shovel"</code>, <code>"axe"</code>) and harvest level. This method is not chainable.</td>
</tr>
</tbody>
</table>
<p>The above methods are chainable, unless otherwise stated, meaning they <code>return this</code> to facilitate calling them in series.</p>
<h3 id="advanced-items">Advanced Items<a class="headerlink" href="#advanced-items" title="Permanent link"> </a></h3>
<p>Setting the properties of an item as above only works for simple items. If you want more complicated items, you should subclass <code>Item</code> and override its methods.</p>
<h2 id="registering-an-item">Registering an Item<a class="headerlink" href="#registering-an-item" title="Permanent link"> </a></h2>
<p>Items must be <a href="../../concepts/registries/index.htm#registering-things">registered</a> to function.</p>
</article>