---
linkTitle: IBakedModel
---

<article class="docs-entry">
<h1 id="ibakedmodel"><code>IBakedModel</code><a class="headerlink" href="#ibakedmodel" title="Permanent link"> </a></h1>
<p><code>IBakedModel</code> is the result of calling <a href="../imodel/index.htm#bake"><code>IModel::bake</code></a>. Unlike <code>IModel</code>, which purely represents a shape without any concept of items or blocks, <code>IBakedModel</code> is not as abstract; it represents geometry that has been optimized and reduced to a form where it is (almost) ready to go to the GPU. It can also process the state of an item or block to change the model.</p>
<p>In a majority of cases, it is not really necessary to implement this interface manually. One can instead use one of the existing implementations.</p>
<h3 id="getoverrides"><code>getOverrides</code><a class="headerlink" href="#getoverrides" title="Permanent link"> </a></h3>
<p>Returns the <a href="../itemoverridelist/index.htm"><code>ItemOverrideList</code></a> to use for this model. This is only used if this model is being rendered as an item.</p>
<h3 id="isambientocclusion"><code>isAmbientOcclusion</code><a class="headerlink" href="#isambientocclusion" title="Permanent link"> </a></h3>
<p>If the model is being rendered as a block in the world, the block in question does not emit any light, and ambient occlusion is enabled, this causes the model to be rendered with ambient occlusion.</p>
<h3 id="isgui3d"><code>isGui3D</code><a class="headerlink" href="#isgui3d" title="Permanent link"> </a></h3>
<p>If the model is being rendered as an item in an inventory, on the ground as an entity, on an item frame, etc. this makes it look &ldquo;flat.&rdquo; In GUIs this also disables the lighting.</p>
<h3 id="isbuiltinrenderer"><code>isBuiltInRenderer</code><a class="headerlink" href="#isbuiltinrenderer" title="Permanent link"> </a></h3>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Unless you know what you&rsquo;re doing and are OK with using deprecated features, just <code>return false</code> from this and continue on.</p>
</div>
<p>When rendering this as an item, returning <code>true</code> causes the model to not be rendered, instead falling back to <code>TileEntityItemStackRenderer::renderItem</code>. For certain vanilla items such as chests and banners, this method is hardcoded to copy data from the item into a <code>TileEntity</code>, before using a <code>TileEntitySpecialRenderer</code> to render that TE in place of the item. For all other items, it will use the <code>TileEntityItemStackRenderer</code> instance provided by <code>Item#setTileEntityItemStackRenderer</code>; refer to <a href="../../../rendering/teisr/index.htm">TileEntityItemStackRenderer</a> page for more information.</p>
<h3 id="getparticletexture"><code>getParticleTexture</code><a class="headerlink" href="#getparticletexture" title="Permanent link"> </a></h3>
<p>Whatever texture should be used for the particles. For blocks, this shows when an entity falls on it, when it breaks, etc. For items, this shows when it breaks or when it&rsquo;s eaten.</p>
<h3 id="getitemcameratransforms"><s><code>getItemCameraTransforms</code></s><a class="headerlink" href="#getitemcameratransforms" title="Permanent link"> </a></h3>
<p>Deprecated in favor of implementing <code>handlePerspective</code>. The default implementation is fine if <code>handlePerspective</code> is implmented. See <a href="../perspective/index.htm">Perspective</a>.</p>
<h3 id="handleperspective"><code>handlePerspective</code><a class="headerlink" href="#handleperspective" title="Permanent link"> </a></h3>
<p>See <a href="../perspective/index.htm">Perspective</a>.</p>
<h3 id="getquads"><code>getQuads</code><a class="headerlink" href="#getquads" title="Permanent link"> </a></h3>
<p>This is the main method of <code>IBakedModel</code>. It returns <code>BakedQuad</code>s, which contain the low-level vertex data that will be used to render the model. If the model is being rendered as a block, then the <code>IBlockState</code> passed in is non-null. Additionally, <a href="../extended-blockstates/index.htm"><code>Block::getExtendedState</code></a> is called to create the passed <code>IBlockState</code>, which allows for arbitrary data to be passed from the block to the model. If the model is being rendered as an item, the <code>ItemOverrideList</code> returned from <code>getOverrides</code> is responsible for handling the state of the item, and the <code>IBlockState</code> parameter will be <code>null</code>.</p>
<p>The <code>EnumFacing</code> passed in is used for face culling. If the block against the given side of the block being rendered is opaque, then the faces associated with that side are not rendered. If that parameter is <code>null</code>, all faces not associated with a side are returned (that will never be culled).</p>
<p>Note that this method is called very often: once for every combination of non-culled face and supported block render layer (anywhere between 0 to 28 times) <em>per block in world</em>. This method should be as fast as possible, and should probably cache heavily.</p>
<p>The <code>long</code> parameter is a random number.</p>
</article>