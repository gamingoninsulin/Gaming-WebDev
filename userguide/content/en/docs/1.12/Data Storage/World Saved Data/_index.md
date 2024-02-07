---
linkTitle: World Saved Data
---

<article class="docs-entry">
<h1 id="world-saved-data">World Saved Data<a class="headerlink" href="#world-saved-data" title="Permanent link"> </a></h1>
<p>The World Saved Data system allows attaching data to worlds, either per dimension, or global.</p>
<h2 id="declaration">Declaration<a class="headerlink" href="#declaration" title="Permanent link"> </a></h2>
<p>The basis of the system is the <code>WorldSavedData</code> class. This class provides the basic methods used to manage the data:</p>
<ul>
<li><code>writeToNBT</code>: Allows the implementation to write data to the world.</li>
<li><code>readFromNBT</code>: Allows the implementation to load previously saved data.</li>
<li><code>markDirty</code>: This method is not overridden by the implementation. Instead, it must be called after changing the data, to notify Minecraft that there are changes that need to be written. If not called, the existing data will be kept instead, and <code>writeToNBT</code> will not get called.</li>
</ul>
<p>An implementation will override this class, and instances of this implementation will be attached to the <code>World</code> objects, ready to store any required data.</p>
<p>A basic skeleton may look like this:</p>
<pre class="highlight"><code class="language-Java">public class ExampleWorldSavedData extends WorldSavedData {
  private static final String DATA_NAME = MODID + "_ExampleData";

  // Required constructors
  public ExampleWorldSavedData() {
    super(DATA_NAME);
  }
  public ExampleWorldSavedData(String s) {
    super(s);
  }

  // WorldSavedData methods
}</code></pre>

<h2 id="registration-and-usage">Registration and Usage<a class="headerlink" href="#registration-and-usage" title="Permanent link"> </a></h2>
<p>The WorldSavedData is loaded and/or attached to the world on demand. A good practice is to create a static get method that will load the data, and if not present, attach a new instance.</p>
<p>There are two ways to attach the data: per dimension, or globally. Global data will be attached to a shared map, that will be obtainable from any instance of the World class, while per-world data will not be shared across dimensions. Keep in mind the separation between client and server, as they get separate instances of global data, so if data is needed on both sides, manual synchronization will be required.</p>
<p>In code, these storage locations are represented by two instances of <code>MapStorage</code> present in the World object. The global data is obtained from <code>World#getMapStorage()</code>, while the per-world map is obtained from <code>World#getPerWorldStorage()</code>.</p>
<p>The existing data can be obtained using <code>MapStorage#getOrLoadData</code>, and new data can be attached using <code>MapStorage#setData</code>.</p>
<pre class="highlight"><code class="language-Java">public static ExampleWorldSavedData get(World world) {
  // The IS_GLOBAL constant is there for clarity, and should be simplified into the right branch.
  MapStorage storage = IS_GLOBAL ? world.getMapStorage() : world.getPerWorldStorage();
  ExampleWorldSavedData instance = (ExampleWorldSavedData) storage.getOrLoadData(ExampleWorldSavedData.class, DATA_NAME);

  if (instance == null) {
    instance = new ExampleWorldSavedData();
    storage.setData(DATA_NAME, instance);
  }
  return instance;
}</code></pre>
</article>