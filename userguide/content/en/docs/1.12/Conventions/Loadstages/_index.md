---
linkTitle: LoadStages
---

<article class="docs-entry">
<h1 id="loading-stages">Loading Stages<a class="headerlink" href="#loading-stages" title="Permanent link"> </a></h1>
<p>Forge loads your mod in 3 main stages: Pre-Initialization, Initialization, and Post-Initialization, commonly referred to as preInit, init, and postInit.
There are some other events that are important too, depending on what your mod does.
Each of these stages occurs at a different point in the loading stage and thus what can be safely done in each stage varies.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Loading stage events can only be used in your <code>@Mod</code> class, in methods marked with the <code>@EventHandler</code> annotation.</p>
</div>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>Many objects (e.g. Blocks, Items, Recipes, etc.) that were previously registered in Pre-Initialization, or other stage event handlers, should now be registered via <a href="../../concepts/registries/index.htm#registering-things">registry events</a>.
This is to pave the way to being able to reload mods dynamically at runtime, which can&rsquo;t be done using loading stages (as they are fired once upon application startup).
RegistryEvents are fired after Pre-Initialization.</p>
</div>
<h2 id="pre-initialization">Pre-Initialization<a class="headerlink" href="#pre-initialization" title="Permanent link"> </a></h2>
<p>Pre Init is the place set anything up that is required by your own or other mods.
This stage&rsquo;s event is the <code>FMLPreInitializationEvent</code>.
Common actions to preform in preInit are:</p>
<ul>
<li>Creating and reading the config file</li>
<li>Registering <a href="../../datastorage/capabilities/index.htm">Capabilities</a></li>
</ul>
<h2 id="initialization">Initialization<a class="headerlink" href="#initialization" title="Permanent link"> </a></h2>
<p>Init is where to accomplish any game related tasks that rely upon the items and blocks set up in preInit.
This stage&rsquo;s event is the <code>FMLInitializationEvent</code>.
Common actions to preform in init are:</p>
<ul>
<li>Registering world generators</li>
<li>Registering event handlers</li>
<li>Sending IMC messages</li>
</ul>
<h2 id="post-initialization">Post-Initialization<a class="headerlink" href="#post-initialization" title="Permanent link"> </a></h2>
<p>Post Init is where your mod usually does things which rely upon other mods.
This stage&rsquo;s event is the <code>FMLPostInitializationEvent</code>.
Common actions to preform in postInit are:</p>
<ul>
<li>Mod compatibility, or anything which depends on other mods&rsquo; init phases being finished.</li>
</ul>
<h2 id="other-important-events">Other Important Events<a class="headerlink" href="#other-important-events" title="Permanent link"> </a></h2>
<ul>
<li>IMCEvent: Process received IMC Messages</li>
<li>FMLServerStartingEvent: Register Commands</li>
</ul>
</article>