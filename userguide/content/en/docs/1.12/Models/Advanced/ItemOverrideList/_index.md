---
linkTitle: ItemOverrideList
---

<article class="docs-entry">
<h1 id="itemoverridelist"><code>ItemOverrideList</code><a class="headerlink" href="#itemoverridelist" title="Permanent link"> </a></h1>
<p><code>ItemOverrideList</code> provides a way for an <a href="../ibakedmodel/index.htm"><code>IBakedModel</code></a> to process the state of an <code>ItemStack</code> and return a new <code>IBakedModel</code>; thereafter, the returned model replaces the old one. <code>ItemOverrideList</code> represents an arbitrary function <code>(IBakedModel, ItemStack, World, EntityLivingBase)</code> → <code>IBakedModel</code>, making it useful for dynamic models. In vanilla, it is used to implement item property overrides.</p>
<h3 id="itemoverridelist_1"><code>ItemOverrideList()</code><a class="headerlink" href="#itemoverridelist_1" title="Permanent link"> </a></h3>
<p>Given a list of <code>ItemOverride</code>s, the constructor copies that list and stores the copy. The list may be accessed with <code>getOverrides</code>, and it is used to implement the vanilla <code>applyOverride</code>, which, in turn, is used in the vanilla <code>handleItemState</code>.</p>
<h3 id="applyoverride"><s><code>applyOverride</code></s><a class="headerlink" href="#applyoverride" title="Permanent link"> </a></h3>
<p>This is a deprecated vanilla method. It is only called in the vanilla <code>handleItemState</code>, and in almost all cases can be safely ignored.</p>
<h3 id="handleitemstate"><code>handleItemState</code><a class="headerlink" href="#handleitemstate" title="Permanent link"> </a></h3>
<p>This takes an <code>IBakedModel</code>, an <code>ItemStack</code>, a <code>World</code>, and an <code>EntityLivingBase</code> to produce another <code>IBakedModel</code> to use for rendering. This is where models can handle the state of their items.</p>
<p>This should not mutate the world.</p>
<h3 id="getoverrides"><code>getOverrides</code><a class="headerlink" href="#getoverrides" title="Permanent link"> </a></h3>
<p>Returns an immutable list containing all the <a href="#itemoverride"><code>ItemOverride</code></a>s used by this <code>ItemOverrideList</code>. If none are applicable, this returns the empty list.</p>
<h2 id="itemoverride"><code>ItemOverride</code><a class="headerlink" href="#itemoverride" title="Permanent link"> </a></h2>
<p>This class represents a vanilla item override, which holds several predicates for the properties on an item and a model to use in case those predicates are satisfied. They are the objects in the <code>overrides</code> array of a vanilla item JSON model:</p>
<pre class="highlight"><code class="language-json">{
  "__comment": "Inside a vanilla JSON item model.",
  "overrides": [
    {
      "__comment": "This is an ItemOverride.",
      "predicate": {
        "__comment": "This is the Map&lt;ResourceLocation, Float&gt;, containing the names of properties and their minimum values.",
        "example1:prop": 4
      },
      "__comment": "This is the 'location', or target model, of the override, which is used if the predicate above matches.",
      "model": "example1:item/model"
    },
    {
      "__comment": "This is another ItemOverride.",
      "predicate": {
        "prop": 1
      },
      "model": "example2:item/model"
    }
  ]
}</code></pre>
</article>