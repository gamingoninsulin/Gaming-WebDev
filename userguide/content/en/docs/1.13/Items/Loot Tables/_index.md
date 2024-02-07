---
linkTitle: Loot Tables
---

<article class="docs-entry">
<h1 id="loot-tables">Loot Tables<a class="headerlink" href="#loot-tables" title="Permanent link"> </a></h1>
<p>Loot tables are an easy way to generate random loot given random distributions of items. They are used in vanilla to generate random chest loot as well as for mob drops.</p>
<p>The vanilla <a href="https://minecraft.gamepedia.com/Loot_table">wiki</a> describes the loot table JSON format in great detail, so this article instead will focus on code that you might have to write to use and manipulate loot tables in your mod. To get the most out of this article, read the aforementioned <a href="https://minecraft.gamepedia.com/Loot_table">wiki</a> page in its entirety before reading this article.</p>
<h2 id="registering-a-modded-loot-table">Registering a Modded Loot Table<a class="headerlink" href="#registering-a-modded-loot-table" title="Permanent link"> </a></h2>
<p>In order to make Minecraft load and be aware of your loot table, simply call <code>LootTableList.register(new ResourceLocation("modid", "loot_table_name"))</code>, which will resolve and load <code>/assets/modid/loot_tables/loot_table_name.json</code>. This call can be made during any of preinit, init, or postinit. You may organize your tables into folders freely.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Loot pools in mod loot tables must include an additional <code>name</code> tag that uniquely identifies that pool within the table. A common strategy is to name the pool with the kinds of items that its entries contain.
If you specify multiple loot entries with the same <code>name</code> tag (e.g. the same item but with different functions each time), then you must give each of those entries an <code>entryName</code> tag that uniquely identifies that entry within the pool. For <code>name</code> tags that do not clash, then <code>entryName</code> is automatically set to the value of <code>name</code>.
These additional requirements are imposed by Forge to facilitate modification of tables at load time using <code>LootTableLoadEvent</code> (see below).</p>
</div>
<h2 id="registering-custom-objects">Registering Custom Objects<a class="headerlink" href="#registering-custom-objects" title="Permanent link"> </a></h2>
<p>In addition to vanilla&rsquo;s, you can also register your own loot conditions, loot functions, and entity properties.</p>
<p>Entity properties are solely for use of the <code>minecraft:entity_properties</code> loot condition, and are used to test if entities involved in the looting (the looted entity or the killer) have certain properties. The only property in vanilla is <code>minecraft:on_fire</code>.</p>
<p>All three are registered similarly, by calling <code>LootConditionManager.registerCondition</code>, <code>LootFunctionManager.registerFunction()</code>, or <code>EntityPropertyManager.registerProperty()</code>, respectively.</p>
<p>The methods take a <code>Serializer</code> instance, which takes the ID of the object as a <code>ResourceLocation</code>, and the <code>Class</code> implementing the behavior in code - <code>LootCondition</code>, <code>LootFunction</code>, and <code>EntityProperty</code>, respectively.</p>
<p>Since you register the JSON serializer and deserializer, you can require additional fields when using your condition, function, or property. See the vanilla implementations in <code>net.minecraft.world.storage.loot.{conditions, functions, properties}</code> for examples.</p>
<p>Then, in order to use your conditions, functions, or properties, simply specify the registry name you passed to the <code>Serializer</code> constructor. An example loot entry:
<pre class="highlight"><code class="language-javascript">{
    "type": "item",
    "name": "mymod:myitem",
    "conditions": [
        {
            "condition": "mymod:mycondition",
            "foo": 1, // can require custom parameters in deserializer
        },
        {
            "condition": "minecraft:entity_properties",
            "entity": "this",
            "properties": {
                "mymod:my_property": { // structure of the right side is completely up to deserializer
                    "bar": 2
                }
            }
        }
    ],
    "functions": [
        {
            "function": "mymod:myfunction",
            "foobar": 3 // can require custom parameters in deserializer
        }
    ]
}</code></pre>
<h2 id="modifying-vanilla-loot">Modifying Vanilla Loot<a class="headerlink" href="#modifying-vanilla-loot" title="Permanent link"> </a></h2>
<h3 id="overview">Overview<a class="headerlink" href="#overview" title="Permanent link"> </a></h3>
<p>Not only can you specify your own loot tables, conditions, functions, and entity properties, you can also modify others as they load.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Users are allowed by vanilla to place their own loot tables in the world save directory to override the game&rsquo;s (and mods&rsquo;) own tables. These are considered config files and thus cannot be modified by the methods described below, <strong>by design</strong>.</p>
</div>
<p>The entry point to modifying tables at runtime is <code>LootTableLoadEvent</code>, which is fired once for each table loaded. From here, you may query and remove pools by name, or add instances of <code>LootPool</code>. This is why modded loot pools are required to have names.</p>
<p>You might be wondering how we modify vanilla tables, then, since they do not have names. Forge resolves this by generating names for all pools in vanilla tables. The first pool is named <code>main</code>, since many tables only have one pool. Subsequent pools are named by position: <code>pool1</code> for the second pool, <code>pool2</code> for the third, and so on. Removing a pool does not shift the names of the other pools.</p>
<p>Within each <code>LootPool</code>, you may also modify the roll and bonus roll attributes of the pool (how many times the table will call this pool) as well as query and remove entries by name, or add instances of <code>LootEntry</code>.</p>
<p>Similar to the case for pools, entries need unique names for retrieval and removal. Forge resolves this by adding a hidden <code>entryName</code> field to all loot entries. If the entry&rsquo;s <code>name</code> field is unique within the pool, then <code>entryName</code> is automatically set to <code>name</code>. Otherwise, a name must be specified in modded entries, and is automatically generated for vanilla entries. For each repeat, a number is incremented. For example, if there are three entries in a vanilla pool each with <code>name: "minecraft:stick"</code>, then the three <code>entryName</code> tags generated would be <code>minecraft:stick</code>, <code>minecraft:stick#0</code>, and <code>minecraft:stick#1</code>. Likewise, removing an entry does not shift the names of the other entries.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>You must perform all of your desired changes to the table during that table&rsquo;s <code>LootTableLoadEvent</code>, any changes afterward are disallowed by safety checks or will cause undefined behavior if the safety checks are bypassed.</p>
</div>
<h3 id="adding-dungeon-loot">Adding Dungeon Loot<a class="headerlink" href="#adding-dungeon-loot" title="Permanent link"> </a></h3>
<p>Next, an example of one of the most common use cases for modifying vanilla loot: adding dungeon item spawns.</p>
<p>First, listen for the event for the table we want to modify:
<pre class="highlight"><code class="language-Java">@SubscribeEvent
public void lootLoad(LootTableLoadEvent evt) {
    if (evt.getName().toString().equals("minecraft:chests/simple_dungeon")) {
        // do stuff with evt.getTable()
    }
}</code></pre>
<p>In this case, we are adding to the potential spawns, but don&rsquo;t want to interfere with the entry weights of the preexisting pools. The most flexible and simple solution is to add another pool with a single loot entry referencing your own loot table JSON, because loot entries are able to recursively draw from a completely different table.</p>
<p>For example, your mod might include <code>/assets/mymod/loot_tables/inject/simple_dungeon.json</code>:
<pre class="highlight"><code class="language-javascript">{
    "pools": [
        {
            "name": "main",
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:nether_star",
                    "weight": 40
                },
                {
                    "type": "empty",
                    "weight": 60
                }
            ]
        }
    ]
}</code></pre>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>You still need to register this table with <code>LootTableList.register()</code>!</p>
</div>
<p>Then the loot entry and pool are created and added, resulting in a new loot pool for dungeon chests that has a 60% chance of nothing and 40% of a nether star.
<pre class="highlight"><code class="language-Java">LootEntry entry = new LootEntryTable(new ResourceLocation("mymod:inject/simple_dungeon"), &lt;weight&gt;, &lt;quality&gt;, &lt;conditions&gt;, &lt;entryName&gt;); // weight doesn't matter since it's the only entry in the pool. Other params set as you wish.

LootPool pool = new LootPool(new LootEntry[] {entry}, &lt;conditions&gt;, &lt;rolls&gt;, &lt;bonusRolls&gt;, &lt;name&gt;); // Other params set as you wish.

evt.getTable().addPool(pool);</code></pre>
<p>Of course, if the loot you want to add cannot be determined ahead of time, you can freely construct and add <code>LootPool</code>s and implementations of <code>LootEntry</code> in your event handler similar to the calls shown above.</p>
<p>A real-world example of this approach in action can be seen in Botania. The event handler is located <a href="https://github.com/Vazkii/Botania/blob/e38556d265fcf43273c99ea1299a35400bf0c405/src/main/java/vazkii/botania/common/core/loot/LootHandler.java">here</a>, and the injected tables are located <a href="https://github.com/Vazkii/Botania/tree/e38556d265fcf43273c99ea1299a35400bf0c405/src/main/resources/assets/botania/loot_tables/inject">here</a>.</p>
<h2 id="changing-mob-drops">Changing Mob Drops<a class="headerlink" href="#changing-mob-drops" title="Permanent link"> </a></h2>
<p>Subclasses of <code>EntityLiving</code> automatically support drawing from a loot table upon death. This is done by overriding the <code>getLootTable</code> method to return a <code>ResourceLocation</code> to the desired table. This serves as the mob&rsquo;s default table; the tables of both your and other mods&rsquo; mobs can be overridden for a single entity by setting the <code>deathLootTable</code> field of the entity. </p>
<h2 id="generating-loot-in-code">Generating Loot In-Code<a class="headerlink" href="#generating-loot-in-code" title="Permanent link"> </a></h2>
<p>Occasionally, you may want to generate <code>ItemStack</code>s from a loot table from your own code.</p>
<p>First, obtain the loot table itself (you need access to a <code>World</code>):
<pre class="highlight"><code class="language-Java">LootTable table = this.world.getLootTableManager().getLootTableFromLocation(new ResourceLocation("mymod:my_table")); // resolves to /assets/mymod/loot_tables/my_table.json</code></pre>
<p>Next, create a <code>LootContext</code> using the provided <code>LootContextBuilder</code>, which holds information about the context of the looting, such as the killer, luck of the looter, and finishing blow.
<pre class="highlight"><code class="language-Java">LootContext ctx = new LootContext.Builder(world)
    .withLuck(...) // adjust luck, commonly EntityPlayer.getLuck()
    .withLootedEntity(...) // set looted entity
    .withPlayer(...) // set player as killer
    .withDamageSource(...) // pass killing blow and non-player killer
    .build();</code></pre>
<p>Finally, to get a collection of <code>ItemStack</code>s:
<pre class="highlight"><code class="language-Java">List&lt;ItemStack&gt; stacks = table.generateLootForPools(world.rand, ctx);</code></pre>
<p>Or to fill an inventory:
<pre class="highlight"><code class="language-Java">table.fillInventory(iinventory, world.rand, ctx);</code></pre>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This only works with <code>IInventory</code> for now.</p>
</div>
</article>