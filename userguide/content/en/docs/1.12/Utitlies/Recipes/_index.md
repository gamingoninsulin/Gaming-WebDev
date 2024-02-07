---
linkTitle: Recipes
---

<article class="docs-entry">
<h1 id="recipes">Recipes<a class="headerlink" href="#recipes" title="Permanent link"> </a></h1>
<p>With the update to Minecraft 1.12, Mojang introduced a new data-driven recipe system based on JSON files. Since then it has been adopted by Forge as well and will be expanded in Minecraft 1.13 into datapacks.</p>
<h2 id="loading-recipes">Loading Recipes<a class="headerlink" href="#loading-recipes" title="Permanent link"> </a></h2>
<p>Forge will load all recipes which can be found within the <code>./assets/&lt;modid&gt;/recipes/</code> folder. You can call these files whatever you want, thought the vanilla convention is to name them after the output item. This name is also used as the registration key, but does not affect the operation of the recipe.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Recipe files cannot begin with an underscore as this is reserved for constants files. The JSON file extension is required.</p>
</div>
<h2 id="the-recipe-file">The Recipe file<a class="headerlink" href="#the-recipe-file" title="Permanent link"> </a></h2>
<p>A basic recipe file might look like the following example:</p>
<pre class="highlight"><code class="language-json">{
    "type": "minecraft:crafting_shaped",
    "pattern":
    [
        "xxa",
        "x x",
        "xxx"
    ],
    "key":
    {
        "x":
        {
            "type": "forge:ore_dict",
            "ore": "gemDiamond"
        },
        "a":
        {
            "item": "mymod:myfirstitem",
            "data": 1
        }
    },
    "result":
    {
        "item": "mymod:myitem",
        "count": 9,
        "data": 2
    }
}</code></pre>

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>When you first obtain an ingredient to a vanilla recipe it will automatically unlock the recipe in the recipe book. To achieve the same effect, you have to use the <a href="#"><code>Advancement</code></a> system and create a new <code>Advancement</code> for each of your ingredients.</p>
<p>The advancement has to exist. This doesn&rsquo;t mean it has to be visible in the advancement tree.</p>
</div>
<h3 id="type">Type<a class="headerlink" href="#type" title="Permanent link"> </a></h3>
<p>The type of a recipe with the type field. You can think of this as the definition of which crafting layout is to be used, for example <code>minecraft:crafting_shaped</code> or <code>minecraft:crafting_shapeless</code>.</p>
<p>If you want, you can define your own types as well which requires the use of a <a href="#factories"><code>_factories.json</code></a> file.</p>
<h3 id="groups">Groups<a class="headerlink" href="#groups" title="Permanent link"> </a></h3>
<p>Optionally you can add a group to your recipes to be displayed within the recipe helper interface. All recipes with the same group String will be shown in the same group. For example, this can be used to have all door recipes shown in the recipe helper interface as a single entry, even though there are different types of doors.</p>
<h2 id="types-of-crafting-recipes">Types of crafting recipes<a class="headerlink" href="#types-of-crafting-recipes" title="Permanent link"> </a></h2>
<p>Within this section we will take a closer look on the differences between defining a shaped and a shapeless crafting recipe.</p>
<h3 id="shaped-crafting">Shaped crafting<a class="headerlink" href="#shaped-crafting" title="Permanent link"> </a></h3>
<p>Shaped recipes require the <code>pattern</code> and <code>key</code> keywords. A pattern defines the slot an item must appear in using placeholder characters. You can choose whatever character you want to be a placeholder for an item. Keys on the other hand define what items are to be used instead of the placeholders. A key is defined by a placeholder character and the item.
Additional the type <code>forge:ore_dict</code> may be added. This defines the item beeing part of the <a href="../oredictionary/index.htm"><code>OreDictionary</code></a> and can for example be used when it doesn&rsquo;t matter which copper ore is used to produce a copper ingot. In this case the <code>ore</code> tag has to be used instead of the <code>item</code> tag to define the item. There are <a href="https://minecraft.gamepedia.com/Recipe">many more</a> of these types which can be used here and you can even register your own.
The <code>data</code> tag is a optional and used to define the metadata of a block or item.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Any item which uses <code>setHasSubtypes(true)</code> requires the use of the <code>data</code> field. When it is not used within the ingredients or keys, it will mean any metadata of this item will be accepted, for example: Not defining the data of a sword means even a half broken sword will be accepted for the crafting recipe!</p>
</div>
<h3 id="shapeless-crafting">Shapeless crafting<a class="headerlink" href="#shapeless-crafting" title="Permanent link"> </a></h3>
<p>A shapeless recipe doesn&rsquo;t make use of the <code>pattern</code> and <code>key</code> keywords.</p>
<p>To define a shapeless recipe, you have to use the <code>ingredients</code> list. It defines which items have to be used for the crafting process and can also make use of the additional type <code>forge:ore_dict</code> and it&rsquo;s functionality as described above. There are <a href="https://minecraft.gamepedia.com/Recipe">many more</a> of these types which can be used here and you can even register your own. It is even possible to define multiple instances of the same item which means multiple of these items have to be in place for the crafting recipe to take place.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>While there is no limit on how many ingredients your recipe requires the vanilla crafting table does only allow 9 items to be placed for each crafting recipe.</p>
</div>
<p>The following example shows how an ingredient list looks like within JSON.</p>
<pre class="highlight"><code class="language-json">    "ingredients": [
        {
            "type": "forge:ore_dict",
            "ore": "gemDiamond"
        },
        {
            "item": "minecraft:nether_star"
        }
    ],</code></pre>

<h3 id="smelting">Smelting<a class="headerlink" href="#smelting" title="Permanent link"> </a></h3>
<p>To define a recipe for the furnace, you have to use <code>GameRegistry.addSmelting(input, output, exp);</code> as the smelting recipes are currently not JSON based.</p>
<h2 id="recipe-elements">Recipe Elements<a class="headerlink" href="#recipe-elements" title="Permanent link"> </a></h2>
<h3 id="patterns">Patterns<a class="headerlink" href="#patterns" title="Permanent link"> </a></h3>
<p>A pattern will be defined with the <code>pattern</code> list. Each string represents one row in the crafting grid and each placeholder character within the String represents a column. As seen in the example above a space means that no item needs to be inserted at that position.</p>
<h3 id="keys">Keys<a class="headerlink" href="#keys" title="Permanent link"> </a></h3>
<p>A key set is used in combination with patterns and contains keys whose name is the same as the placeholder character in the pattern list which it represents. One key may be defined to represent multiply items as it is the case for the wooden button. This means that the player can use one of the defined items for the crafting recipe, for example different types of wood.</p>
<pre class="highlight"><code class="language-json">  "key": {
     "#": [
      {
        "item": "minecraft:planks",
         "data": 0
      },
      {
        "item": "minecraft:planks",
        "data": 1
      }
    ]
  }</code></pre>

<h3 id="results">Results<a class="headerlink" href="#results" title="Permanent link"> </a></h3>
<p>Every <code>recipe</code> has to have a result tag to define the output item.</p>
<p>When crafting something, you can get out more than one item. This is achieved by defining the <code>count</code> number. If this is left out, meaning it doesn&rsquo;t exist within the result block, it defaults to 1. Negative values are not allowed here as an Itemstack cannot be smaller than 0. There is no option to use the <code>count</code> number anywhere else than for the result.
The <code>data</code> field is a optional and used to define the metadata of a block or item. It defaults to 0 when it doesn&rsquo;t exist.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Any item which uses <code>setHasSubtypes(true)</code> requires the data field. In this case, it is not optional!</p>
</div>
<h2 id="factories">Factories<a class="headerlink" href="#factories" title="Permanent link"> </a></h2>
<p>Factories can be used to allow defining recipes and ingredients of a custom type (class). To create your own factory, create a <code>_factories.json</code>. Within this file a type has to be defined, for example: <code>recipes</code>,  <code>ingredients</code> or <code>conditions</code>. These types represent <code>IRecipeFactory</code>, <code>IIngredientFactory</code>, and <code>IConditionFactory</code>, respectively. The entry &ldquo;key&rdquo; must be a <code>name</code> which can be later used in your recipes, and the &ldquo;value&rdquo; is the fully qualified class name is a class you have to create which implements one of the above recipes. The class must have an empty constructor. For example:</p>
<pre class="highlight"><code class="language-json">{
    "&lt;type&gt;": {
        "&lt;name&gt;": "&lt;fully qualified classname for the specified type&gt;"
    }
}</code></pre>

<div class="admonition note">
<p class="admonition-title">Note</p>
<p>There is no need to create a new <code>_factories.json</code> for each type you want to specify, they can all be defined in a single file.</p>
</div>
<h3 id="conditional-recipes">Conditional Recipes<a class="headerlink" href="#conditional-recipes" title="Permanent link"> </a></h3>
<p>Conditional recipes can be created by making use of the factory system described above. For this you use the <code>conditions</code> type with the <code>IConditionFactory</code> from above and can later add the <code>conditions</code> type to your recipes:</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Conditions will only be checked once at startup!</p>
</div>
<h2 id="constants">Constants<a class="headerlink" href="#constants" title="Permanent link"> </a></h2>
<p>It is possible to define constant values for your recipes. These values have to be defined within a <code>_constants.json</code> and can be used within any recipe of your mod by just writing <code>#&lt;name&gt;</code>. For filled buckets, you should use <code>fluid</code> instead of <code>data</code>. For example, this constant defines <code>#SADDLE</code> which represents the vanilla saddle item.</p>
<pre class="highlight"><code class="language-json">[
    {
        "name": "SADDLE",
        "ingredient": {
            "item": "minecraft:saddle",
            "data": 0
        }
    }
]</code></pre>
</article>