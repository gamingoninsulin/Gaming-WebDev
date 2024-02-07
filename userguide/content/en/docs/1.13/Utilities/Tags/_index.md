---
linkTitle: Tags
---

<article class="docs-entry">
<h1 id="tags">Tags<a class="headerlink" href="#tags" title="Permanent link"> </a></h1>
<p>Tags are generalized sets of objects in the game, used for grouping related things together and providing fast membership checks.</p>
<h2 id="declaring-your-own-groupings">Declaring Your Own Groupings<a class="headerlink" href="#declaring-your-own-groupings" title="Permanent link"> </a></h2>
<p>Tags are declared in your mod&rsquo;s datapack. For example, <code>/data/modid/tags/blocks/foo/tagname.json</code> will declare a <code>Tag&lt;Block&gt;</code> with ID <code>modid:foo/tagname</code>.
Similarly, you may append to or override tags declared in other domains, such as Vanilla, by declaring your own JSONs.
For example, to add your own mod&rsquo;s saplings to the Vanilla sapling tag, you would specify it in <code>/data/minecraft/tags/blocks/saplings.json</code>, and Vanilla will merge everything into one tag at reload, if the <code>replace</code> option is false.
If <code>replace</code> is true, then all entries before the json specifying <code>replace</code> will be removed.
See the <a href="https://minecraft.gamepedia.com/Tag#JSON_format">Vanilla wiki</a> for a description of the base syntax.</p>
<p>Forge provides two extensions on the Vanilla syntax:
* You may declare an <code>optional</code> array of the same format as the <code>values</code> array, but any values listed here that are not present will not cause the tag loading to error.
This is useful to provide integration for mods that may or may not be present at runtime.
* You may declare a <code>remove</code> array of the same format as the <code>values</code> array. Any values listed here will be removed from the tag. This acts as a finer grained version of the Vanilla <code>replace</code> option.</p>
<h2 id="using-tags-in-code">Using Tags In Code<a class="headerlink" href="#using-tags-in-code" title="Permanent link"> </a></h2>
<p>Block, Item, and Fluid tags are automatically sent from the server to any remote clients on login and reload. Function tags are not synced.</p>
<p><code>BlockTags.getCollection()</code> and <code>ItemTags.getCollection()</code> will retrieve the current <code>TagCollection</code>, from which you can retrieve a <code>Tag</code> object by its ID.
With a <code>Tag</code> object in hand, membership can be tested with <code>tag.contains(thing)</code>, or all the objects in the tag queried with <code>tag.getAllElements()</code>.</p>
<p>As an example:
<pre class="highlight"><code class="language-Java">ResourceLocation myTagId = new ResourceLocation("mymod", "myitemgroup");
Item unknownItem = stack.getItem();
boolean isInGroup = ItemTags.getCollection().getOrCreateTag(myTagId).contains(unknownItem);
// alternatively, can use getTag and perform a null check</code></pre>
<p>!!! note:
    The <code>TagCollection</code> returned by <code>getCollection()</code> (and the <code>Tag</code>s within it) may expire if a reload happens, so you should always query the collection anew every time you need it.
    The static <code>Tag</code> fields in <code>BlockTags</code> and <code>ItemTags</code> avoid this by introducing a wrapper that handles this expiring.
    Alternatively, a resource reload listener can be used to refresh any cached tags.</p>
<h2 id="conventions">Conventions<a class="headerlink" href="#conventions" title="Permanent link"> </a></h2>
<p>There are several conventions that will help facilitate compatibility in the ecosystem:
* If there is a Vanilla tag that fits your block or item, add it to that tag. See the <a href="https://minecraft.gamepedia.com/Tag#List_of_tags">list of Vanilla tags</a>.
* If there is a Forge tag that fits your block or item, add it to that tag. The list of tags declared by Forge can be seen on GitHub.
* If there is a group of something you feel should be shared by the community, consider PR-ing it to Forge instead of making your own tag
* Tag naming conventions should follow Vanilla conventions. In particular, item and block groupings are plural instead of singular. E.g. <code>minecraft:logs</code>, <code>minecraft:saplings</code>.
* Item tags should be sorted into subdirectories according to the type of item, e.g. <code>forge:ingots/iron</code>, <code>forge:nuggets/brass</code>, etc.</p>
<h2 id="migration-from-oredictionary">Migration from OreDictionary<a class="headerlink" href="#migration-from-oredictionary" title="Permanent link"> </a></h2>
<ul>
<li>For recipes, tags can be used directly in the vanilla recipe format (see below)</li>
<li>For matching items in code, see the section above.</li>
<li>If you are declaring a new type of item grouping, follow a couple naming conventions:</li>
<li>Use <code>domain:type/material</code>. When the name is a common one that all modders should adopt, use the <code>forge</code> domain.</li>
<li>For example, brass ingots should be registered under the <code>forge:ingots/brass</code> tag, and cobalt nuggets under the <code>forge:nuggets/cobalt</code> tag.</li>
</ul>
<h2 id="using-tags-in-recipes-and-advancements">Using Tags in Recipes and Advancements<a class="headerlink" href="#using-tags-in-recipes-and-advancements" title="Permanent link"> </a></h2>
<p>Tags are directly supported by Vanilla, see the respective Vanilla wiki pages for <a href="https://minecraft.gamepedia.com/Recipe#JSON_format">recipes</a> and <a href="https://minecraft.gamepedia.com/Advancements">advancements</a> for usage details.</p>
</article>