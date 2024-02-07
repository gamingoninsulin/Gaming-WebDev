---
linkTitle: Introduction
---

<article class="docs-entry">
<h1 id="intro-to-advanced-models">Intro to Advanced Models<a class="headerlink" href="#intro-to-advanced-models" title="Permanent link"> </a></h1>
<p>While simple models and blockstates are all well and good, they aren&rsquo;t dynamic. For example, Forge&rsquo;s Universal Bucket can hold all kinds of fluid, which may be mod added. It has to dynamically piece the model together from the base bucket model and the liquid. How is that done? Enter <code>IModel</code>.</p>
<p>In order to understand how this works, let&rsquo;s go through the internals of the model system. Throughout this section, you will probably have to refer to this to grasp a clear understanding of what is happening. This is true in reverse as well. You will likely not understand everything happening here, but as you move through this section you should be able to grasp more and more of it until everything is clear.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>If this is your first time reading through do not skip <em>anything</em>! It is <em>imperative</em> that you read everything <strong>in order</strong> so as to build a comprehensive understanding! In the same vein, if this is your first time reading, do not follow links if they lead to pages further ahead in the section.</p>
</div>
<ol>
<li>
<p>A set of <code>ModelResourceLocation</code>s are marked as models to be loaded through <code>ModelLoader</code>.</p>
<ul>
<li>
<p>For items, their models must be manually marked for loading with <code>ModelLoader.registerItemVariants</code>. (<code>ModelLoader.setCustomModelResourceLocation</code> does this.)</p>
</li>
<li>
<p>For blocks, their statemappers produce a <code>Map&lt;IBlockState, ModelResourceLocation&gt;</code>. All blocks are iterated, and then the values of this map are marked to be loaded.</p>
</li>
</ul>
</li>
<li>
<p><a href="#"><code>IModel</code></a>s are loaded from each <code>ModelResourceLocation</code> and cached in a <code>Map&lt;ModelResourceLocation, IModel&gt;</code>.</p>
<ul>
<li>
<p>An <code>IModel</code> is loaded from the only <a href="../icustommodelloader/index.htm"><code>ICustomModelLoader</code></a> that accepts it. (Multiple loaders attempting to load a model will cause a <code>LoaderException</code>.) If none is found and the <code>ResourceLocation</code> is actually a <code>ModelResourceLocation</code> (that is, this is not a normal model; it&rsquo;s actually a blockstate variant), it goes to the blockstate loader (<code>VariantLoader</code>). Otherwise the model is a normal vanilla JSON model and is loaded the vanilla way (<code>VanillaLoader</code>).</p>
</li>
<li>
<p>A vanilla JSON model (<code>models/item/*.json</code> or <code>models/block/*.json</code>), when loaded, is a <code>ModelBlock</code> (yes, even for items). This is a vanilla class that is not related to <code>IModel</code> in any way. To rectify this, it gets wrapped into a <code>VanillaModelWrapper</code>, which <em>does</em> implement <code>IModel</code>.</p>
</li>
<li>
<p>A vanilla/Forge blockstate variant, when loaded, first loads the entire blockstate JSON it comes from. The JSON is deserialized into a <code>ModelBlockDefinition</code> that is then cached to the path of the JSON. A list of variant definitions is then extracted from the <code>ModelBlockDefinition</code> and placed into a <code>WeightedRandomModel</code>.</p>
</li>
<li>
<p>When loading a vanilla JSON item model (<code>models/item/*.json</code>), the model is requested from a <code>ModelResourceLocation</code> with variant <code>inventory</code> (e.g. the dirt block item model is <code>minecraft:dirt#inventory</code>); thereby causing the model to be loaded by <code>VariantLoader</code> (as it is a <code>ModelResourceLocation</code>). When <code>VariantLoader</code> fails to load the model from the blockstate JSON, it falls back to <code>VanillaLoader</code>.</p>
<ul>
<li>The most important side-effect of this is that if an error occurs in <code>VariantLoader</code>, it will try to also load the model via <code>VanillaLoader</code>. If this also fails, then the resulting exception produces <em>two</em> stacktraces. The first is the <code>VanillaLoader</code> stacktrace, and the second is from <code>VariantLoader</code>. When debugging model errors, it is important to ensure that the right stacktrace is being analyzed.</li>
</ul>
</li>
<li>
<p>An <code>IModel</code> can be loaded from a <code>ResourceLocation</code> or retrieved from the cache by invoking <code>ModelLoaderRegistry.getModel</code> or one of the exception handling alternatives.</p>
</li>
</ul>
</li>
<li>
<p>All texture dependencies of the loaded models are loaded and stitched into the atlas. The atlas is a giant texture that contains all the model textures pasted together. When a model refers to a texture, during rendering, an extra UV offset is applied to match the texture&rsquo;s position in the atlas.</p>
</li>
<li>
<p>Every model is baked by calling <code>model.bake(model.getDefaultState(), ...)</code>. The resulting <a href="#"><code>IBakedModel</code></a>s are cached in a <code>Map&lt;ModelResourceLocation, IBakedModel&gt;</code>.</p>
</li>
<li>
<p>This map is then stored in the <code>ModelManager</code>. The <code>ModelManager</code> for an instance of the game is stored in <code>Minecraft::modelManager</code>, which is private with no getter.</p>
<ul>
<li>
<p>The <code>ModelManager</code> may be acquired, without reflection or access tranformation, through <code>Minecraft.getMinecraft().getRenderItem().getItemModelMesher().getModelManager()</code> or <code>Minecraft.getMinecraft().getBlockRenderDispatcher().getBlockModelShapes().getModelManager()</code>. Contrary to their names, these are equivalent.</p>
</li>
<li>
<p>One may request an <code>IBakedModel</code> from the cache (without loading and/or baking models, only accessing the existing cache) in a <code>ModelManager</code> with <code>ModelManager::getModel</code>.</p>
</li>
</ul>
</li>
<li>
<p>Eventually, an <code>IBakedModel</code> will be rendered. This is done by calling <code>IBakedModel::getQuads</code>. The result is a list of <code>BakedQuad</code>s (quadrilaterals: polygons with 4 vertices). These can then be passed to the GPU for rendering. Items and blocks diverge a bit here, but it&rsquo;s relatively simple to follow.</p>
<ul>
<li>
<p>Items in vanilla have properties and overrides. To facilitate this, <code>IBakedModel</code> defines <code>getOverrides</code>, which returns an <code>ItemOverrideList</code>. <code>ItemOverrideList</code> defines <code>handleItemState</code>, which takes the original model, the entity, world, and stack, to find the final model. Overrides are applied before all other operations on the model, including <code>getQuads</code>. As <code>IBlockState</code> is not applicable to items, <code>IBakedModel::getQuads</code> receives <code>null</code> as its state parameter when being rendered as an item.</p>
</li>
<li>
<p>Blocks have blockstates, and when a block&rsquo;s <code>IBakedModel</code> is being rendered, the <code>IBlockState</code> is passed directly into the <code>getQuads</code> method. In the context of models only, blockstates can have an extra set of properties, known as <a href="../extended-blockstates/index.htm">unlisted properties</a>.</p>
</li>
</ul>
</li>
</ol>
</article>