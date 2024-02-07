---
linkTitle: Locations
---

<article class="docs-entry">
<h1 id="locations">Locations<a class="headerlink" href="#locations" title="Permanent link"> </a></h1>
<p>Minecraft expects certain parts of your project to be in certain locations, such as textures and JSONs.</p>
<p>All locations and items covered in this page are relative to your <code>./src/main/resources/</code> folder.</p>
<h3 id="mcmodinfo">mcmod.info<a class="headerlink" href="#mcmodinfo" title="Permanent link"> </a></h3>
<p>The <code>mcmod.info</code> file is in the root directory.</p>
<h3 id="blockstates">Blockstates<a class="headerlink" href="#blockstates" title="Permanent link"> </a></h3>
<p>Blockstate definition files are in the JSON format and are in the <code>./assets/&lt;modid&gt;/blockstates/</code> folder.</p>
<h3 id="localizations">Localizations<a class="headerlink" href="#localizations" title="Permanent link"> </a></h3>
<p>Localizations are plain-text files with the file extension <code>.lang</code> and the name being their <a href="../../../../en-us/previous-versions/commerce-server/ee825488%28v%3Dcs.20%29.html">language code</a> such as <code>en_US</code>.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>If a <code>pack_format</code> of 3 is specified in a <code>pack.mcmeta</code> file, the language code should be lowercase.</p>
</div>
<p>They are located in the <code>./assets/&lt;modid&gt;/lang/</code> folder.</p>
<h3 id="models">Models<a class="headerlink" href="#models" title="Permanent link"> </a></h3>
<p>Model files are in JSON format and are located in <code>./assets/&lt;modid&gt;/models/block/</code> or <code>./assets/&lt;modid&gt;/models/item/</code> depending on whether they are for a block or an item, respectively.</p>
<h3 id="textures">Textures<a class="headerlink" href="#textures" title="Permanent link"> </a></h3>
<p>Textures are in the PNG format and are located in <code>./assets/&lt;modid&gt;/textures/blocks/</code> or <code>./assets/&lt;modid&gt;/textures/items/</code> depending on whether they are for a block or an item, respectively.</p>
<h3 id="recipes">Recipes<a class="headerlink" href="#recipes" title="Permanent link"> </a></h3>
<p><a href="../../utilities/recipes/index.htm">Recipes</a> are in JSON format and are located in <code>./assets/&lt;modid&gt;/recipes/</code>.</p>
</article>