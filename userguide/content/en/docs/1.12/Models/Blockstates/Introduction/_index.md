---
linkTitle: Introduction
---

<article class="docs-entry">
<h1 id="introduction-to-blockstate-jsons">Introduction to Blockstate JSONs<a class="headerlink" href="#introduction-to-blockstate-jsons" title="Permanent link"> </a></h1>
<p>Blockstate JSONs are Minecraft&rsquo;s way to map &ldquo;variant strings&rdquo; to models. A variant string can be absolutely anything, from &ldquo;inventory&rdquo; to &ldquo;power=5&rdquo; to &ldquo;I am your father.&rdquo; They represent an actual model, where the blockstate is just a container for them. In code, a variant string within a blockstate JSON is represented by a <code>ModelResourceLocation</code>.</p>
<p>When the game searches for a model corresponding to a block in the world, it takes the <a href="../../../blocks/states/index.htm">blockstate</a> for that position, and then it uses an <code>IStateMapper</code> to find the corresponding <code>ModelResourceLocation</code> for it, which then refers to the actual model. The default <code>IStateMapper</code> uses the block&rsquo;s registry name as the location of the blockstate JSON. (E.g. block <code>examplemod:testblock</code> goes to the <code>ResourceLocation</code> <code>examplemod:testblock</code>.) The variant string is pieced together from the blockstate&rsquo;s properties. More information can be found <a href="../../using/index.htm#block-models">here</a>.</p>
<p>As an example, let&rsquo;s take a look at the vanilla <code>oak_log.json</code>:</p>
<pre class="highlight"><code class="language-json">{
  "variants": {
    "axis=y":  { "model": "oak_log" },
    "axis=z":   { "model": "oak_log_side" },
    "axis=x":   { "model": "oak_log_side", "y": 90 },
    "axis=none":   { "model": "oak_bark" }
  }
}</code></pre>

<p>Here we define 4 variant strings, and for each we use a certain model, either the upright log, the sideways log (rotated or not), and the all bark model (this model is not seen normally in vanilla; you have to use <code>/setblock</code> to create it). Since logs use the default <code>IStateMapper</code>, these variants will define the look of a log depending on the property <code>axis</code>.</p>
<p>A blockstate always has to be defined for all possible variant strings. When you have many properties, this results in lots of possible variants, as every combination of properties must be defined. In Minecraft 1.8&rsquo;s blockstate format, you have to define every string explicitly, which leads to long, complicated files. It also doesn&rsquo;t support the concept of submodels, or multiple models in the same blockstate. In order to allievate this, Forge introduced its <a href="../forgeBlockstates/index.htm">own blockstate format</a>, which is available in Minecraft 1.8 and up.</p>
<p>Starting from Minecraft 1.9, Mojang also introduced the &ldquo;multipart&rdquo; format. You can find a definition of its format on the <a href="../../../../../wiki/Model.html#Block_states">wiki</a>. Forge&rsquo;s format and the multipart format are not better than each other; they each cover different use cases and it is your choice which one you want to use.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The Forge format is really more like syntactic sugar for automatically calculating the set of all possible variants for you behind the scenes. This allows you to use the resulting <code>ModelResourceLocation</code>s for things other than blocks. (<a href="../../using/index.htm#blockstate-jsons-for-items">Such as items</a>. This is also true of the 1.8 format, but there is almost no reason to use that format.) The 1.9 format is a more complicated system that depends on having an <code>IBlockState</code> to pick the model. It will not directly work in other contexts without some code around it.</p>
</div>
<p>For reference, here&rsquo;s an excerpt from the 1.8 blockstate for fences, <code>fence.json</code>:</p>
<pre class="highlight"><code class="language-json">"east=true,north=false,south=false,west=false": { "model": "oak_fence_n", "y": 90, "uvlock": true }</code></pre>

<p>This is just one variant out of 16. Even worse, there are 6 models for fences, one each for no connections, one connection, two connections in a straight line, two perpendicular connections, three connections, and one for all four connections.</p>
<p>Here&rsquo;s an excerpt from the same file in 1.9, which uses the multipart format:</p>
<pre class="highlight"><code class="language-json">{ "when": { "east": "true" },
  "apply": { "model": "oak_fence_side", "y": 90, "uvlock": true }
}</code></pre>

<p>This is one case of 5. You can read this as &ldquo;when east=true, use the model oak_fence_side rotated 90 degrees&rdquo;. This allows the final model to be built up from 5 smaller parts, 4 of which (the connections) are conditional and the 5th being the unconditional central post. This uses only two models, one for the post, and one for the side connection.</p>
</article>