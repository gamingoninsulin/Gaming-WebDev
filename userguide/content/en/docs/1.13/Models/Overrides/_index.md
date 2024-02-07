---
linkTitle: Overrides
---

<article class="docs-entry">
<h1 id="item-property-overrides">Item Property Overrides<a class="headerlink" href="#item-property-overrides" title="Permanent link"> </a></h1>
<p>Item properties are a way for the &ldquo;properties&rdquo; of items to be exposed to the model system. An example is the bow, where the most important property is how far the bow has been pulled. This information is then used to choose a model for the bow, creating an animation for pulling it. This is different from assigning <code>ModelResourceLocation</code>s directly to items through <code>ModelLoader.setCustomModelResourceLocation</code> or <code>ModelLoader.setCustomMeshDefinition</code>. These methods fix the possible set of models. Used on a bow, for example, these methods would permanently fix the number of frames in the pull animation to 4. However, properties are more flexible.</p>
<p>An item property assigns a certain <code>float</code> value to every <code>ItemStack</code> it is registered for, and vanilla item model definitions can use these values to define &ldquo;overrides&rdquo;, where an item defaults to a certain model, but if an override matches, it overrides the model and uses another. The format of item models, including overrides, can be found on the [wiki][]. They are useful mainly because of the fact that they are continuous. For example, bows use item properties to define their pull animation. Since the value of the property is a <code>float</code>, it increases continuously from 0 to 1. This allows resource packs to add as many models as they want for the bow pulling animation along that spectrum, instead of being stuck with four &ldquo;slots&rdquo; for their models in the animation. The same is true of the compass and clock.</p>
<h2 id="adding-properties-to-items">Adding Properties to Items<a class="headerlink" href="#adding-properties-to-items" title="Permanent link"> </a></h2>
<p><code>Item::addPropertyOverride</code> is used to add a property to an item. The <code>ResourceLocation</code> parameter is the name given to the property (e.g. <code>new ResourceLocation("pull")</code>). The <code>IItemPropertyGetter</code> is a function that takes the <code>ItemStack</code>, the <code>World</code> it&rsquo;s in, and the <code>EntityLivingBase</code> that holds it, returning the <code>float</code> value for the property. Some examples are the <code>"pulling"</code> and &ldquo;<code>pull</code>&rdquo; properties in <code>ItemBow</code>, and the several <code>static final</code> ones in <code>Item</code>. For modded item properties, it is recommended that the modid of the mod is used as the namespace (e.g. <code>examplemod:property</code> and not just <code>property</code>, as that really means <code>minecraft:property</code>).</p>
<h2 id="using-overrides">Using Overrides<a class="headerlink" href="#using-overrides" title="Permanent link"> </a></h2>
<p>The format of an override can be seen on the <a href="https://minecraft.gamepedia.com/Model#Item_models">wiki</a>, and a good example can be found in <code>model/item/bow.json</code>. For reference, here is a hypothetical example of an item with an <code>examplemod:power</code> property. If the values have no match, the default is the current model.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>A predicate applies to all values <em>greater than or equal to</em> the given value.</p>
</div>
<pre class="highlight"><code class="language-json">{
  "parent": "item/generated",
  "textures": {
    "__comment": "Default",
    "layer0": "examplemod:items/examplePartial"
  },
  "overrides": [
    {
      "__comment": "power &gt;= .75",
      "predicate": {
        "examplemod:power": 0.75
      },
      "model": "examplemod:item/examplePowered"
    }
  ]
}</code></pre>

<p>And here&rsquo;s a hypothetical snippet from the supporting code. (This does not have to be client-only; it will work on a server too. In vanilla, properties are registered in the item&rsquo;s constructor.)</p>
<pre class="highlight"><code class="language-java">item.addPropertyOverride(new IItemPropertyGetter() {
  @SideOnly(Side.CLIENT)
  @Override
  public float apply(ItemStack stack, @Nullable World world, @Nullable EntityLivingBase entity) {
    return (float)getPowerLevel(stack) / (float)getMaxPower(stack); // Some external methods
  }
}</code></pre>
</article>