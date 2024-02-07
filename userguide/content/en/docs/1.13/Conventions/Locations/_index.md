---
linkTitle: Locations
---

<article class="docs-entry">
<h1 id="locations">Locations<a class="headerlink" href="#locations" title="Permanent link"> </a></h1>
<p>Minecraft expects certain parts of your project to be in certain locations, such as textures and JSONs.</p>
<p>All locations and items covered in this page are relative to your <code>./src/main/resources/</code> folder.</p>
<h3 id="modstoml">mods.toml<a class="headerlink" href="#modstoml" title="Permanent link"> </a></h3>
<p>The <code>mods.toml</code> file is in the <code>./META-INF/</code> directory.</p>
<h3 id="blockstates">Blockstates<a class="headerlink" href="#blockstates" title="Permanent link"> </a></h3>
<p>Blockstate definition files are in the JSON format and are in the <code>./assets/&lt;modid&gt;/blockstates/</code> folder.</p>
<h3 id="localizations">Localizations<a class="headerlink" href="#localizations" title="Permanent link"> </a></h3>
<p><a href="../../concepts/internationalization/index.htm">Localizations</a> are plain-text files with the file extension <code>.json</code> and the name being their <a href="https://msdn.microsoft.com/en-us/library/ee825488(v=cs.20).aspx">language code</a> in lowercase such as <code>en_us</code>.</p>
<p>They are located in the <code>./assets/&lt;modid&gt;/lang/</code> folder.</p>
<h3 id="models">Models<a class="headerlink" href="#models" title="Permanent link"> </a></h3>
<p>Model files are in JSON format and are located in <code>./assets/&lt;modid&gt;/models/block/</code> or <code>./assets/&lt;modid&gt;/models/item/</code> depending on whether they are for a block or an item, respectively.</p>
<h3 id="textures">Textures<a class="headerlink" href="#textures" title="Permanent link"> </a></h3>
<p>Textures are in the PNG format and are located in <code>./assets/&lt;modid&gt;/textures/blocks/</code> or <code>./assets/&lt;modid&gt;/textures/items/</code> depending on whether they are for a block or an item, respectively.</p>
<h3 id="recipes">Recipes<a class="headerlink" href="#recipes" title="Permanent link"> </a></h3>
<p><a href="../../utilities/recipes/index.htm">Recipes</a> are in JSON format and are located in <code>./data/&lt;modid&gt;/recipes/</code>.</p>
</article>