---
linkTitle: Using
---

<article class="docs-entry">
<h1 id="connecting-blocks-and-items-to-models">Connecting Blocks and Items to Models<a class="headerlink" href="#connecting-blocks-and-items-to-models" title="Permanent link"> </a></h1>
<h2 id="block-models">Block Models<a class="headerlink" href="#block-models" title="Permanent link"> </a></h2>
<p>Blocks are not directly linked to models, instead, block<em>states</em> are mapped to <code>ModelResourceLocation</code>s, which point to models (&ldquo;models&rdquo; includes blockstate JSONs). An <code>IBlockState</code> is mapped to a <code>ModelResourceLocation</code> by an <code>IStateMapper</code>. The default statemapper, which works by default for all blocks, works as follows:</p>
<ol>
<li>Get the registry name of the blockstate&rsquo;s block.</li>
<li>Set said name as the <code>ResourceLocation</code> part of the <code>ModelResourceLocation</code>.</li>
<li>Get all properties and their values in the blockstate.</li>
<li>Get the name of each property with <code>IProperty#getName</code>.</li>
<li>Get the name of each value with <code>IProperty&lt;T&gt;#getName(T)</code>.</li>
<li>Sort the pairs alphabetically by the name of the <em>property only</em>.</li>
<li>Produce a comma delimited string of key-value pairs (e.g. <code>a=b,c=d,e=f</code>).</li>
<li>Set that as the variant part of the <code>ModelResourceLocation</code>.</li>
<li>If the variant string is empty (i.e. no properties defined), default the variant to <code>normal</code>.</li>
</ol>
<p>The variant string is silently <code>toLowerCase</code>d, so if a statemapper returns <code>mod:model#VARIANT</code>, the game will query the JSON for the string &ldquo;variant&rdquo;, not &ldquo;VARIANT.&rdquo;</p>
<h3 id="custom-istatemappers">Custom <code>IStateMapper</code>s<a class="headerlink" href="#custom-istatemappers" title="Permanent link"> </a></h3>
<p>It&rsquo;s simple to use a custom <code>IStateMapper</code>. Once an instance is acquired, it may be registered with a call to <code>ModelLoader.setCustomStateMapper</code>. <code>IStateMapper</code>s are registered per block, so this method receives both the <code>IStateMapper</code> and the block it works on. There is also a builder <code>StateMap.Builder</code> for some common use cases.</p>
<h4 id="statemapbuilder"><code>StateMap.Builder</code><a class="headerlink" href="#statemapbuilder" title="Permanent link"> </a></h4>
<p>The builder <code>StateMap.Builder</code> can create <code>IStateMapper</code>s for some of the most common use cases. Once one is instantiated, methods can be called to set its parameters, and a call to <code>build</code> will generate an <code>IStateMapper</code> using those parameters.</p>
<h5 id="withname"><code>withName</code><a class="headerlink" href="#withname" title="Permanent link"> </a></h5>
<p><code>withName</code> takes a property as argument and sets that as the &ldquo;name&rdquo; (actually the path) of the returned <code>ModelResourceLocation</code>. When the resulting <code>IStateMapper</code> is applied to a blockstate, it takes the value of the property given, then finds the name for that value, and uses that as the resource path. It is clearer with an example:</p>
<pre class="highlight"><code class="language-java">PropertyDirection PROP_FACING = PropertyDirection.create("facing"); // Start with a property
IStateMapper mapper = new StateMap.Builder().withName(PROP_FACING).build(); // Use the builder</code></pre>

<p>Now, if <code>mapper</code> is asked to find the <code>ModelResourceLocation</code> for the blockstate <code>examplemod:block1[facing=east]</code>, it will map it to <code>examplemod:east#normal</code>. Given <code>examplemod:block2[color=red,facing=north]</code>, it will map it to <code>examplemod:north#color=red</code>.</p>
<h5 id="withsuffix"><code>withSuffix</code><a class="headerlink" href="#withsuffix" title="Permanent link"> </a></h5>
<p>The suffix is a plain string that gets tacked on to the end of the resource path. For example, if the suffix is set to <code>_suff</code>, the resulting <code>IStateMapper</code> will map the blockstate <code>examplemod:block[facing=east]</code> to the <code>ModelResourceLocation</code> <code>examplemod:block_suff#facing=east</code>.</p>
<h5 id="ignore"><code>ignore</code><a class="headerlink" href="#ignore" title="Permanent link"> </a></h5>
<p>This causes the <code>IStateMapper</code> to simply ignore the given properties when mapping a blockstate. When called twice, the two lists are merged. An example:</p>
<pre class="highlight"><code class="language-java">PropertyDirection PROP_OUT = PropertyDirection.create("out");
PropertyDirection PROP_IN = PropertyDirection.create("in");
// These two are equivalent
IStateMapper together = new StateMap.Builder().ignore(PROP_OUT, PROP_IN).build();
IStateMapper merged = new StateMap.Builder().ignore(PROP_OUT).ignore(PROP_IN).build();</code></pre>

<p>When either <code>together</code> or <code>merged</code> are asked to map the blockstate <code>examplemod:block1[in=north,out=south]</code>, they&rsquo;ll give the <code>ModelResourceLocation</code> <code>examplemod:block1#normal</code>. Given <code>examplemod:block2[in=north,out=south,color=blue]</code>, they&rsquo;ll produce <code>examplemod:block2#color=blue</code>. Finally, given <code>examplemod:block3[color=white,out=east]</code> (no <code>in</code>), they&rsquo;ll produce <code>examplemod:block3#color=white</code>.</p>
<h2 id="item-models">Item Models<a class="headerlink" href="#item-models" title="Permanent link"> </a></h2>
<p>Unlike blocks, which automatically have a default <code>IStateMapper</code> that works without any extra registration, items must be registered to their models manually. This is done through <code>ModelLoader.setCustomModelResourceLocation</code>. This method takes the item, a metadata value, and a <code>ModelResourceLocation</code>, and registers a mapping so that all <code>ItemStack</code>s with the item and metadata given use the given <code>ModelResourceLocation</code> for their model. The way the game searches for the model is as follows:</p>
<ol>
<li>For a <code>ModelResourceLocation</code> <code>&lt;namespace&gt;:&lt;path&gt;#&lt;varstr&gt;</code></li>
<li>Attempt to find a custom model loader that volunteers to load this model.</li>
<li>If that succeeds, load the model with the found loader and break out of these instructions.</li>
<li>If that fails, attempt to load it from the blockstate JSON loader.</li>
<li>If that fails, attempt to load it from the vanilla JSON loader (which loads the model <code>assets/&lt;namespace&gt;/models/item/&lt;path&gt;.json</code>).</li>
</ol>
<p>JSON item models from <code>models/item</code> can also leverage <a href="../overrides/index.htm">overrides</a>.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p><code>ModelLoader.setCustomModelResourceLocation</code> also calls <code>ModelLoader.registerItemVariants</code> with the item and <code>ModelResourceLocation</code> given. This sets up the model for baking later.</p>
</div>
<h3 id="itemmeshdefinition"><code>ItemMeshDefinition</code><a class="headerlink" href="#itemmeshdefinition" title="Permanent link"> </a></h3>
<p>An <code>ItemMeshDefinition</code> is a function that takes <code>ItemStack</code>s and maps them to <code>ModelResourceLocation</code>s. They are registered per item, and this can be done with <code>ModelLoader.setCustomMeshDefinition</code>, which takes an item and the <code>ItemMeshDefinition</code> to use for its <code>ItemStack</code>s.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p><code>ModelLoader.setCustomMeshDefinition</code> <strong>does not</strong> call <code>ModelLoader.registerItemVariants</code>. Therefore, <code>ModelLoader.registerItemVariants</code> method must be passed every <code>ModelResourceLocation</code> the <code>ItemMeshDefinition</code> can return in order for it to work.</p>
</div>
<h3 id="blockstate-jsons-for-items">Blockstate JSONs for Items<a class="headerlink" href="#blockstate-jsons-for-items" title="Permanent link"> </a></h3>
<p>Note that <em>items</em> can use <em>block</em>state JSONs. This is possible by simply passing a <code>ModelResourceLocation</code> pointing to a blockstate JSON into <code>ModelLoader.setCustomModelResourceLocation</code> or returning it from an <code>ItemMeshDefinition</code>. Doing so allows the model to take advantage of things like submodels and combining variants. The two main use cases are items that share their models with blocks (especially <code>ItemBlock</code>s) and the default item layer model (The <code>textures</code> block inside combining variant definitions can be used to build up the layers of the model, with one property setting <code>layer0</code>, another setting <code>layer1</code>, etc.).</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>1.9 multipart blockstates will not work as item models out of the box, as they require an <code>IBlockState</code> to select a model.</p>
</div>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>There is one major caveat. Blockstate JSONs can only resolve paths to models under <code>models/block</code>; they cannot see models under <code>models/item</code> (even using <code>../item</code> causes an error). This means that the <code>minecraft:item/generated</code> model (which sets the default transforms for items) cannot be used in a blockstate JSON. As a workaround, the <code>minecraft:builtin/generated</code> model can be used instead and the transforms set with the <code>transform</code> tag in the blockstate JSON. (Block models that inherit from <code>minecraft:block/block</code> already set transforms, so this isn&rsquo;t necessary for them.) Here&rsquo;s an example:</p>
<pre class="highlight"><code class="language-json">"defaults": {
  "model": "builtin/generated",
  "__comment": "Get Forge to inject the default rotations and scales for an item in a player's hand, on the ground, etc.",
  "transform": "forge:default-item"
}</code></pre>

</div>
</article>