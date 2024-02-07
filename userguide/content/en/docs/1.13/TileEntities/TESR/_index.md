---
linkTitle: TESR
---

<article class="docs-entry">
<h1 id="tileentityrenderer">TileEntityRenderer<a class="headerlink" href="#tileentityrenderer" title="Permanent link"> </a></h1>
<p>A <code>TileEntityRenderer</code> or <code>TER</code> (previously <code>TileEntitySpecialRenderer</code> or <code>TESR</code>) is used to render blocks in a way that cannot be represented with a static baked model (JSON, OBJ, B3D, others). A tile entity renderer requires the block to have a TileEntity.</p>
<p>By default OpenGL (via <code>GlStateManager</code>) is used to handle rendering in a TER. See the OpenGL documentation to learn more. It is recommended to use a <code>TileEntityRendererFast</code> instead whenever possible.</p>
<h2 id="creating-a-ter">Creating a TER<a class="headerlink" href="#creating-a-ter" title="Permanent link"> </a></h2>
<p>To create a TER, create a class that inherits from <code>TileEntityRenderer</code>. It takes a generic argument specifying the block&rsquo;s TileEntity class. The generic argument is used in the TER&rsquo;s <code>render</code> method.</p>
<p>Only one TER exists for a given tile entity. Therefore, values that are specific to a single instance in the world should be stored in the tile entity being passed to the renderer rather than in the TER itself. For example, an integer that increments every frame, if stored in the TER, will increment every frame for every tile entity of this type in the world.</p>
<h3 id="render"><code>render</code><a class="headerlink" href="#render" title="Permanent link"> </a></h3>
<p>This method is called every frame in order to render the tile entity. </p>
<h4 id="parameters">Parameters<a class="headerlink" href="#parameters" title="Permanent link"> </a></h4>
<ul>
<li><code>tileentity</code>: This is the instance of the tile entity being rendered.</li>
<li><code>x</code>, <code>y</code>, <code>z</code>: The position at which the tile entity should be rendered.</li>
<li><code>partialTicks</code>: The amount of time, in fractions of a tick, that has passed since the last full tick.</li>
<li><code>destroyStage</code>: The destroy stage for the block if it is being broken.</li>
</ul>
<h2 id="registering-a-ter">Registering a TER<a class="headerlink" href="#registering-a-ter" title="Permanent link"> </a></h2>
<p>In order to register a TESR, call <code>ClientRegistry#bindTileEntitySpecialRenderer</code> passing the tile entity class to be renderer with this TER and the instance of the TER to use to render all TEs of this class.</p>
<h2 id="tileentityrendererfast"><code>TileEntityRendererFast</code><a class="headerlink" href="#tileentityrendererfast" title="Permanent link"> </a></h2>
<p>A TER can opt-in to being a &ldquo;fast&rdquo; renderer by extending the <code>TileEntityRendererFast</code> class instead of <code>TileEntityRenderer</code> and returning true from <code>IForgeTileEntity#hasFastRenderer</code>. Instead of implementing <code>render</code>, <code>renderTileEntityFast</code> must be implemented.</p>
<p>A fast TER can offer performance improvements over a traditional TER and should be used wherever possible. This is due to the fact that all fast TER instances are batched together and only issue <em>one</em> combined draw call for all fast TERs per frame to the GPU. This advantage comes at the cost of making direct OpenGL access via <code>GlStateManager</code> or the <code>GLXX</code> classes impossible. Instead a fast TER must only add vertices to the provided <code>VertexBuffer</code>, which represents the combined vertex data for all fast TERs. This allows rendering <code>IBakedModel</code>s. An example can be found in Forge&rsquo;s <code>TileEntityRendererAnimation</code>.</p>
</article>