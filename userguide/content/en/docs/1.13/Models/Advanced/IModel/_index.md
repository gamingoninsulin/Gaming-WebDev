---
linkTitle: IModel
---

<article class="docs-entry">
<h1 id="imodel"><code>IModel</code><a class="headerlink" href="#imodel" title="Permanent link"> </a></h1>
<p><code>IModel</code> is a type that represents a model in its raw state. This is how a model is represented right after it has been loaded. Usually this directly represents the source of the model (e.g. an object deserialized from JSON, or an OBJ container).</p>
<p>At this high level, a model has no concept of items, blocks, or anything of that sort; it purely represents a shape.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p><code>IModel</code> is immutable. Methods such as <code>process</code> that alter the model should never mutate the <code>IModel</code>, as they should construct new <code>IModel</code>s instead.</p>
</div>
<h3 id="getdependencies"><code>getDependencies</code><a class="headerlink" href="#getdependencies" title="Permanent link"> </a></h3>
<p>This is a collection of the <code>ResourceLocation</code>s of all the models this model depends on. These models are guaranteed to be loaded before this one is baked. For example, a model deserialized from a blockstate JSON will depend on the models defined within. Only models that are directly mapped to a block/item are loaded normally; to ensure loading of other models, they must be declared as dependencies of another. Cyclic dependencies will cause a <code>LoaderException</code> to be thrown.</p>
<h3 id="gettextures"><code>getTextures</code><a class="headerlink" href="#gettextures" title="Permanent link"> </a></h3>
<p>This is a collection of the <code>ResourceLocation</code>s of all the textures this model depends on. These textures are guaranteed to be loaded before this model is baked. For example, a vanilla JSON model depends on all the textures defined within.</p>
<h3 id="bake"><code>bake</code><a class="headerlink" href="#bake" title="Permanent link"> </a></h3>
<p>This is the main method of <code>IModel</code>. It takes an <a href="../imodelstate%20part/index.htm"><code>IModelState</code></a>, a <code>VertexFormat</code>, and a function <code>ResourceLocation</code> → <code>TextureAtlasSprite</code>, to return an <a href="../ibakedmodel/index.htm"><code>IBakedModel</code></a>. <code>IBakedModel</code> is less abstract than <code>IModel</code>, and it is what interacts with blocks and items. The function <code>ResourceLocation → TextureAtlasSprite</code> is used to get textures from <code>ResourceLocation</code>s (i.e. the <code>ResourceLocation</code>s of textures are passed to this function and the returned <code>TextureAtlasSprite</code> contains the texture).</p>
<h3 id="process"><code>process</code><a class="headerlink" href="#process" title="Permanent link"> </a></h3>
<p>This method allows a model to process extra data from external sources. The Forge blockstate variant format provides a way to define this data in the resource pack. Within the Forge blockstate format, the property that is used to pass this data is called <code>custom</code>. First, an example:</p>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "defaults": {
    "custom": {
      "__comment": "These can be any JSON primitives with any names, but models should only use what they understand.",
      "meaningOfLife": 42,
      "showQuestion": false
    },
    "model": "examplemod:life_meaning"
  },
  "variants": {
    "dying": {
      "true": {
        "__comment": "Custom data is inherited. Therefore, here `meaningOfLife` is inherited but `showQuestion` is overriden. The model itself remains inherited.",
        "custom": {
          "showQuestion": true
        }
      },
      "false": {}
    }
  }
}</code></pre>

<p>As seen above, custom data can be of any type. Additionally, it is inherited from the defaults into the variants. The custom data is passed in as an <code>ImmutableMap&lt;String, String&gt;</code>. This is a map where the keys are the property names (in the above example, &ldquo;meaningOfLife&rdquo;, &ldquo;showQuestion&rdquo;, and &ldquo;title&rdquo;). Astute observers may notice that numeric and boolean data were defined in within the blockstate but this method only receives <code>String</code>s. This is because all data is converted into strings before being processed. If a model does not understand what a property means, it should just ignore it.</p>
<h3 id="smoothlighting"><code>smoothLighting</code><a class="headerlink" href="#smoothlighting" title="Permanent link"> </a></h3>
<p>In vanilla, smooth lighting enables ambient occlusion. This flag can be controlled by the <code>smooth_lighting</code> property in a Forge blockstate (which can appear wherever a <code>model</code> property can and is inherited). The default implementation does nothing.</p>
<h3 id="gui3d"><code>gui3D</code><a class="headerlink" href="#gui3d" title="Permanent link"> </a></h3>
<p><code>gui3D</code> controls whether a model looks &ldquo;flat&rdquo; in certain positions (e.g. with <code>gui3d</code> set to <code>true</code>, <code>EntityItem</code> renders a stack with multiple items as several layers of the model. With <code>gui3d</code> set to <code>false</code>, the item is always one layer), and also controls lighting inside GUIs. This flag can be controlled by the <code>gui3d</code> property in a Forge blockstate. The default implementation does nothing.</p>
<h3 id="retexture"><code>retexture</code><a class="headerlink" href="#retexture" title="Permanent link"> </a></h3>
<p>This method is used to change the textures a model might use. This is similar to how texture variables in vanilla JSON models work. A model can start out with certain faces with certain textures, and then by setting/overriding texture variables these faces can be changed. An example:</p>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "defaults": {
    "textures": {
      "varA": "examplemod:items/hgttg",
      "varB": "examplemod:blocks/earth",
      "varC": "#varA",
      "varZ": null
    },
    "model": "examplemod:universe"
  }
}</code></pre>

<p>In this example, the <code>textures</code> block will be deserialized as-is into an <code>ImmutableMap</code> with the exception that <code>null</code>s are turned into <code>""</code> (i.e. the final result is <code>"varA" → "examplemod:items/hgttg", "varB" → "examplemod:blocks/earth", "varC" → "#varA", "varZ" → ""</code>). Then, <code>retexture</code> is called to change the textures as needed. How this is done is up to the model. It may be advisable, however, to support resolving texture variables such as &ldquo;#var&rdquo; (like vanilla JSON models) instead of taking them literally. The default implementation does nothing.</p>
<h3 id="uvlock"><code>uvlock</code><a class="headerlink" href="#uvlock" title="Permanent link"> </a></h3>
<p>This method is used to toggle UV lock. UV lock means that when the model itself rotates, the textures applied to the model do not rotate with it. The default implementation does nothing. This can be controlled with the <code>uvlock</code> property in a Forge blockstate. An example:</p>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "defaults": {
    "model": "minecraft:half_slab",
    "textures": {
      "__comment": "Texture definitions here..."
    }
  },
  "variants": {
    "a": [{ "__comment": "No change" }],
    "b": [{
      "__comment": "This is like literally taking the slab and flipping it upside down. The 'side' texture on the side faces is cropped to the bottom half and rotated 180 degrees, just as if a real object were turned upside down.",
      "x": 180
    }],
    "c": [{
      "__comment": "Now this is more interesting. The UV vertices are altered so that the texture won't rotate with the model, so that the side faces have the side texture rightside up and cropped to the top half.",
      "x": 180,
      "uvlock": true
    }]
  }
}</code></pre>
</article>