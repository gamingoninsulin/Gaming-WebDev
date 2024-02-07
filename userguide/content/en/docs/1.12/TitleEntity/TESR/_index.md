---
linkTitle: TESR
---

<article class="docs-entry">
<h1 id="tileentityspecialrenderer">TileEntitySpecialRenderer<a class="headerlink" href="#tileentityspecialrenderer" title="Permanent link"> </a></h1>
<p>A <code>TileEntitySpecialRenderer</code> (TESR) is used to render blocks in a way that cannot be represented with a static baked model (JSON, OBJ, B3D, others). A TESR requires the block to have a TileEntity.</p>
<p>By default OpenGL (via <code>GlStateManager</code>) is used to handle rendering in a TESR. See the OpenGL documentation to learn more. It is recommended to use a FastTESR instead whenever possible.</p>
<h2 id="creating-a-tesr">Creating a TESR<a class="headerlink" href="#creating-a-tesr" title="Permanent link"> </a></h2>
<p>To create a TESR, create a class that inherits from <code>TileEntitySpecialRenderer</code>. It takes a generic argument specifying the block&rsquo;s TileEntity class. The generic argument is used in the TESR&rsquo;s <code>renderTileEntityAt</code> method.</p>
<p>Only one TESR exists for a given tile entity. Therefore, values that are specific to a single instance in the world should be stored in the tile entity being passed to the renderer rather than in the TESR itself. For example, an integer that increments every frame, if stored in the TESR, will increment every frame for every tile entity of this type in the world.</p>
<h3 id="rendertileentityat"><code>renderTileEntityAt</code><a class="headerlink" href="#rendertileentityat" title="Permanent link"> </a></h3>
<p>This method is called every frame in order to render the tile entity. </p>
<h4 id="parameters">Parameters<a class="headerlink" href="#parameters" title="Permanent link"> </a></h4>
<ul>
<li><code>tileentity</code>: This is the instance of the tile entity being rendered.</li>
<li><code>x</code>, <code>y</code>, <code>z</code>: The position at which the tile entity should be rendered.</li>
<li><code>partialTicks</code>: The amount of time, in fractions of a tick, that has passed since the last full tick.</li>
<li><code>destroyStage</code>: The destroy stage for the block if it is being broken.</li>
</ul>
<h2 id="registering-a-tesr">Registering a TESR<a class="headerlink" href="#registering-a-tesr" title="Permanent link"> </a></h2>
<p>In order to register a TESR, call <code>ClientRegistry#bindTileEntitySpecialRenderer</code> passing the tile entity class to be renderer with this TESR and the instance of the TESR to use to render all TEs of this class.</p>
<h2 id="fasttesr"><code>FastTESR</code><a class="headerlink" href="#fasttesr" title="Permanent link"> </a></h2>
<p>A TESR can opt-in to being a FastTESR by extending the <code>FastTESR</code> class instead of <code>TileEntitySpecialRenderer</code> and returning true from <code>TileEntity#hasFastRenderer</code>. Instead of implementing <code>renderTileEntityAt</code>, <code>renderTileEntityFast</code> must be implemented.</p>
<p>A FastTESR can offer performance improvements over a traditional TESR and should be used wherever possible. This is due to the fact that all FastTESR instances are batched together and only issue <em>one</em> combined draw call for all FastTESRs per frame to the GPU. This advantage comes at the cost of making direct OpenGL access via <code>GlStateManager</code> or the <code>GLXX</code> classes impossible. Instead a FastTESR must only add vertices to the provided <code>VertexBuffer</code>, which represents the combined vertex data for all FastTESRs. This allows rendering <code>IBakedModel</code>s. An example can be found in Forge&rsquo;s <code>AnimationTESR</code>.</p>
</article>