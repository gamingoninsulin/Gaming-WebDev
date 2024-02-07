---
linkTitle: ForgeBlockStates
---

<article class="docs-entry">
<h1 id="forges-blockstates">Forge&rsquo;s Blockstates<a class="headerlink" href="#forges-blockstates" title="Permanent link"> </a></h1>
<p>Forge has its own blockstate json format to accommodate for modders needs. It introduces submodels, which allows you to build the final blockstate from different parts.</p>
<div class="admonition attention">
<p class="admonition-title">Attention</p>
<p>Note that all models and textures referenced are from vanilla minecraft. For your own mod, you have to use the full location! For example: <code>"mymod:blocks/blockTexture"</code>.</p>
<p>You don&rsquo;t have to use Forge&rsquo;s blockstate format at all, you can also use the vanilla format!</p>
</div>
<h2 id="general-structure-of-the-format">General Structure of the Format<a class="headerlink" href="#general-structure-of-the-format" title="Permanent link"> </a></h2>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "defaults": {
    "textures": {
      "all": "blocks/dirt"
    },
    "model": "cube_all",
    "uvlock": true
  },
  "variants": {
    "normal": [{}]
  }
}</code></pre>

<p>This json declares a simple blockstate that has dirt on each side. Let&rsquo;s go through it step by step.</p>
<pre class="highlight"><code class="language-json">  "forge_marker": 1,</code></pre>

<p>This tells the game that the blockstate json is the one from Forge, not from vanilla Minecraft.
The 1 is the version of the format, which ensures that old blockstate JSONs can be supported should the format ever change. Currently there is only this one.</p>
<pre class="highlight"><code class="language-json">  "defaults": {
    "textures": {
      "all": "blocks/dirt"
    },
    "model": "cube_all",
    "uvlock": true
  }</code></pre>

<p>The defaults section contains the default values for all variants. They can be overwritten by the variants. The defaults section is <strong>optional</strong>! You do not need to define defaults, the block can be omitted altogether.</p>
<pre class="highlight"><code class="language-json">  "variants": {
    "normal": [{}]
  }</code></pre>

<p>This defines all variants for the block. The simple dirt block only has its default, the <em>normal</em> variant. It does not contain any additional information in this case. Everything that is defined in defaults could also be defined here. For example:</p>
<pre class="highlight"><code class="language-json">  "normal": [{
    "textures": {
      "side": "blocks/cobblestone",
      "end": "blocks/dirt"
    },
    "model": "cube_column"
  }]</code></pre>

<p>This normal variant would use the <em>cube_column</em> model with cobblestone on the sides and dirt on top and bottom.</p>
<p>An entry in the <code>variants</code> section either defines a <a href="../../../blocks/states/index.htm">blockstate</a> property or a plain variant. A property definition is of the form:</p>
<pre class="highlight"><code class="language-json">    "variants": {
      "property_name": {
        "value0": {},
        "value1": {},
        "__comment": "Etc."
      }
   }</code></pre>

<p>A given blockstate can have any number of these. When the blockstate is loaded, the values within each property are used to create all possible variants for the block. The above would create two variants, <code>property_name=value0</code> and <code>property_name=value1</code>. If there were two properties, it would create variants <code>prop1=value11,prop2=value21</code>, <code>prop1=value12,prop2=value21</code>, <code>prop1=value11,prop2=value22</code>, and so on (where the property names are sorted alphabetically). Each such variant is the union of all the variant definitions that went into it. For example, given:</p>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "variants": {
    "shiny": {
      "true":  { "textures": { "all": "some:shiny_texture" } },
      "false": { "textures": { "all": "some:flat_texture"  } }
    },
    "broken": {
      "true":  { "model": "some:broken_model" },
      "false": { "model": "some:intact_model" }
    }
 }
}</code></pre>

<p>The variant &ldquo;broken=false,shiny=true&rdquo; takes the &ldquo;some:intact_model&rdquo; from <code>variants.broken.true.model</code>, and the <code>some:shiny_texture</code> from <code>variants.shiny.true.textures</code>.</p>
<p>An entry can also be a plain variant, like:</p>
<pre class="highlight"><code class="language-json">    "variants": {
      "normal": { "model": "some:model" }
    }</code></pre>

<p>This kind of definition defines a variant &ldquo;normal&rdquo; directly, without forming combinations with those listed in the property-value format. It still inherits from a &ldquo;defaults&rdquo; block, if present, and if the property-value formatted variants generate a variant with the same name, the directly defined variant combines with and overrides values from it. If the variant is defined as a list, then each element is a variant definition, and the one that will be used is random:</p>
<pre class="highlight"><code class="language-json">    "defaults": { "model": "some:model" }
    "variants": {
      "__comment": "When used, the model will have a 75% chance of being rotated.",
      "normal": [{ "y": 0 }, { "y": 90 }, { "y": 180 }, { "y": 270 }]
    }</code></pre>

<p>A property definition is disambiguated from a straight variant by the type of the first entry. If the first entry of <code>variants.&lt;something&gt;</code> is an object, then it is a property definition. If it is anything else, it is a straight variant. In order to avoid mixups, it is recommended to wrap straight variants in a list with one element:</p>
<pre class="highlight"><code class="language-json">   "variants": {
     "simple": [{
       "custom": {},
       "model": "some:model",
       "__comment": "Without the list, the custom: {} would make Forge think this was a property definition."
     }]
   }</code></pre>

<h2 id="sub-models">Sub-Models<a class="headerlink" href="#sub-models" title="Permanent link"> </a></h2>
<p>To show the use of submodels we will create a model that has different variants. Each variant will use submodels to create a different model.
The model will be a pressure plate, and depending on its state it will have parts added to it.</p>
<pre class="highlight"><code class="language-json">{
  "forge_marker": 1,
  "defaults": {
    "textures": {
      "texture": "blocks/planks_oak",
      "wall": "blocks/planks_oak"
    },
    "model": "pressure_plate_up",
    "uvlock": true
  },
  "variants": {
    "__comment": "mossy is a boolean property.",
    "mossy": {
      "true": {
        "__comment": "If true it changes the pressure plate from oak planks to mossy cobble.",
        "textures": {
          "texture": "blocks/cobblestone_mossy"
        }
      },
      "false": {
        "__comment": "Change nothing. The entry has to be here so the Forge blockstate loader knows to generate this variant."
      }
    },
    "__comment": "pillarcount is a property that determines how many pillar submodels we have. Ranges from 0 to 2.",
    "pillarcount": {
      "0": {
        "__comment": "No pillar. Remember, this empty definition has to be here."
      },
      "1": {
        "__comment": "If it is true, it will add the wall model and combine it with the pressure plate.",
        "submodel": "wall_n"
      },
      "2": {
        "textures": {
          "wall": "blocks/cobblestone"
        },
        "submodel": {
          "pillar1": { "model": "wall_n" },
          "pillar2": { "model": "wall_n", "y": 90 }
        }
      }
    }
  }
}</code></pre>

<p>The comments already explain the details on the separate parts, but here&rsquo;s how it works overall: The block definition in code has two properties. One boolean property named <code>mossy</code> and one integer property named <code>pillarCount</code>.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Notice here that the string used in the json is <strong>lowercase</strong>. It has to be lowercase or it won&rsquo;t be found.</p>
</div>
<p>Instead of defining &ldquo;this combination of properties gives model X&rdquo; we say &ldquo;<strong>this</strong> value for this property has <strong>that</strong> impact on the model&rdquo;. In this example it&rsquo;s quite straight forward:</p>
<ul>
<li>If <code>mossy</code> is <code>true</code>, the pressure plate uses the mossy cobblestone texture</li>
<li>If <code>pillarCount</code> is <code>1</code> it will add one wall with connection facing north. The default texture for the wall is oak-planks.</li>
<li>If <code>pillarCount</code> is <code>2</code> it will add two walls, both facing north. However the second wall will be rotated by 90 degree. This showcases that you do not need separate model with Forge&rsquo;s system. You only need once and rotate it around the Y axis. Additionally the texture of the walls is changed to cobblestone.</li>
<li>If <code>pillarCount</code> is <code>0</code> no walls will be added.</li>
</ul>
<p>And here is the result of our work:</p>
<p><img alt="The model in different variations" src="../example.png"></p>
</article>