---
linkTitle: TEISR
---
<article class="docs-entry">
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The features used here only exist in forge versions &gt;= 14.23.2.2638.</p>
</div>
<p>TileEntityItemStackRenderer is a method to use OpenGL to render on items.  This system is much simpler than the old TESRItemStack system, which required a TileEntity, and did not allow access to the ItemStack.</p>
<h2 id="using-tileentityitemstackrenderer">Using TileEntityItemStackRenderer<a class="headerlink" href="#using-tileentityitemstackrenderer" title="Permanent link"> </a></h2>
<p>TileEntityItemStackRenderer allows you to render your item using <code>public void renderByItem(ItemStack itemStackIn)</code>.<br>
There is an overload that takes partialTicks as a parameter, but it is never called in vanilla.</p>
<p>In order to use a TEISR, the Item must first satisfy the condition that its model returns true for <code>IBakedModel#isBuiltInRenderer</code>.
Once that returns true, the Item&rsquo;s TEISR will be accessed for rendering.  If it does not have one, it will use the default <code>TileEntityItemStackRenderer.instance</code>.</p>
<p>To set the TEISR for an Item, use <code>Item#setTileEntityItemStackRenderer</code>.  Each Item can only ever provide one TEISR, and the getter is final so that mods do not return new instances each frame.</p>
<p>That&rsquo;s it, no additional setup is necessary to use a TEISR.</p>
<p>If you need to access the TransformType for rendering, you can store the one passed through <code>IBakedModel#handlePerspective</code>, and use it during rendering.  This method will always be called before <code>TileEntityItemStackRenderer#renderByItem</code>.</p>
</article>