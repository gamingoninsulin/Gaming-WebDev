---
linkTitle: Files
---

<article class="docs-entry">
<h1 id="model-files">Model Files<a class="headerlink" href="#model-files" title="Permanent link"> </a></h1>
<p>A &ldquo;model&rdquo; is simply a shape. It can be a simple cube, it can be several cubes, it can be a truncated icosidodecahedron, or anything in between. Most models you&rsquo;ll see will be in the vanilla JSON format. Models in other formats are loaded into <code>IModel</code>s by an <code>ICustomModelLoader</code> at runtime. Forge provides default implementations for WaveFront OBJ files and Blitz3D files. Most things do not care about what loaded the model or what format it&rsquo;s in as they all implement a common interface, <code>IModel</code>, in code.</p>
<p>When <code>ResourceLocation</code> refers to a model, the path is normally relative to <code>models</code> (e.g. <code>examplemod:block/block</code> → <code>assets/examplemod/models/block/block</code>). A notable exception is within a <a href="../blockstates/introduction/index.htm">blockstate JSON</a> (all 3 formats), where model paths are relative to <code>models/block</code> (e.g. <code>examplemod:block</code> → <code>assets/examplemod/models/block/block</code>).</p>
<p>Block and item models differ in a few ways, the major one being <a href="../overrides/index.htm">item property overrides</a>.</p>
<h2 id="textures">Textures<a class="headerlink" href="#textures" title="Permanent link"> </a></h2>
<p>Textures, like models, are contained within resource packs and are referred to with <code>ResourceLocation</code>s. When <code>ResourceLocation</code>s refer to textures in models, the paths are taken to be relative to <code>textures/</code> (e.g. <code>examplemod:blocks/test</code> → <code>assets/examplemod/textures/blocks/test.png</code>). Additionally, in Minecraft, the <a href="https://en.wikipedia.org/wiki/UV_mapping">UV coordinates</a> (0,0) are taken to mean the <strong>top-left</strong> corner. UVs are <em>always</em> from 0 to 16. If a texture is larger or smaller, the coordinates are scaled to fit. A texture must also be square, and the side length of a texture should be a power of two, as doing otherwise breaks mipmapping. (E.g. 1x1, 2x2, 8x8, 16x16, and 128x128 are good. 5x5 and 30x30 are not recommended because they are not powers of 2. 5x10 and 4x8 are completely broken as they are not square.) If there is an <code>mcmeta</code> file associated with the texture, and an animation is defined, the image can be rectangular and is interpreted as a vertical sequence of square regions from top to bottom, where each square is a frame of the animation.</p>
<h2 id="json-models">JSON Models<a class="headerlink" href="#json-models" title="Permanent link"> </a></h2>
<p>Vanilla Minecraft&rsquo;s JSON model format is rather simple. It defines cuboid (cube/rectangular prism) elements, and assigns textures to their faces. On the <a href="https://minecraft.gamepedia.com/Model#Block_models">wiki</a>, there is a definition of its format.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>JSON models only support cuboid elements; there is no way to express a triangular wedge or anything like it. To have more complicated models, another format must be used.</p>
</div>
<p>When a <code>ResourceLocation</code> refers to the location of a JSON model, it is not suffixed with <code>.json</code>, unlike OBJ and B3D models (e.g. <code>minecraft:block/cube_all</code>, not <code>minecraft:block/cube_all.json</code>).</p>
<h2 id="wavefront-obj-models">WaveFront OBJ Models<a class="headerlink" href="#wavefront-obj-models" title="Permanent link"> </a></h2>
<p>Forge adds a loader for the <code>.obj</code> file format. To use these models, the resource namespace must be registered through <code>OBJLoader.addDomain</code>. This loader accepts any model location that is in a registered namespace and whose path ends in <code>.obj</code>. The <code>.mtl</code> file should be placed next to the <code>.obj</code> and will automatically be used with the <code>.obj</code>. The <code>.mtl</code> file will probably have to be manually edited to change the paths pointing to textures into Minecraft <code>ResourceLocation</code>s. Additinally, the V axis for textures may be flipped depending on the external program that created the model (i.e. V = 0 may be the bottom edge, not the top). This may be rectified in the modelling program itself or done in a Forge blockstate JSON like so:</p>
<pre class="highlight"><code class="language-json">{
  "__comment": "Add the following line on the same level as a 'model' declaration.",
  "custom": { "flip-v": true },
  "model": "examplemod:model.obj"
}</code></pre>

<h2 id="blitz3d-models">Blitz3D Models<a class="headerlink" href="#blitz3d-models" title="Permanent link"> </a></h2>
<p>Forge adds a loader for the <code>.b3d</code> file format. To use these models, the resource namespace must be registered through <code>B3DLoader.addDomain</code>. This loader accepts any model location that is in a registered namespace and whose path ends in <code>.b3d</code>.</p>
</article>