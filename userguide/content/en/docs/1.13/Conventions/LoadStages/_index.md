---
linkTitle: LoadStages
---

<article class="docs-entry">
<h1 id="loading-stages">Loading Stages<a class="headerlink" href="#loading-stages" title="Permanent link"> </a></h1>
<p>The Forge loading process has four main phases. All of these events shown are fired on the mod-specific eventbus, <em>not</em> the global Forge event bus <code>MinecraftForge.EVENT_BUS</code>
Handlers should be registered either using <code>@EventBusSubscriber(bus = Bus.MOD)</code> or in the mod object constructor as follows:</p>
<pre class="highlight"><code class="language-Java">@Mod("mymod")
public class MyMod {
  public MyMod() {
    FMLModLoadingContext.get().getModEventBus().registerListener(this::commonSetup);
  } 

  private void commonSetup(FMLCommonSetupEvent evt) { ... }
}</code></pre>

<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>All four of the below events are called for all mods in parallel. That is, all mods will concurrently receive common setup, FML will wait for 
them all to finish, then all mods will concurrently receive sided setup, and so forth.
Mods <em>must</em> take care to be thread safe, especially when calling other mods&rsquo; API&rsquo;s and accessing Vanilla systems, which are not thread safe in general. This can be done using the <code>DeferredWorkQueue</code> class.</p>
</div>
<h2 id="setup">Setup<a class="headerlink" href="#setup" title="Permanent link"> </a></h2>
<p><code>FMLCommonSetupEvent</code> is the first to fire, and is fired early in the Minecraft starting process.
<a href="../../concepts/registries/index.htm#registering-things">Registry events</a> are fired before this event, so you can expect all registry objects to be valid by the time this runs.
Common actions to perform in common setup are:</p>
<ul>
<li>Creating and reading the config files</li>
<li>Registering <a href="../../datastorage/capabilities/index.htm">Capabilities</a></li>
</ul>
<h2 id="sided-setup">Sided Setup<a class="headerlink" href="#sided-setup" title="Permanent link"> </a></h2>
<p><code>FMLClientSetupEvent</code> and <code>FMLDedicatedServerSetupEvent</code> are fired after common setup, and are where physical side-specific initialization should occur.
Common actions to perform here are registering client-side only things such as key bindings.</p>
<h2 id="imc-enqueue">IMC Enqueue<a class="headerlink" href="#imc-enqueue" title="Permanent link"> </a></h2>
<p>Here, mods should send messages to all other mods they are interested in integrating with, using the <code>InterModComms.sendTo()</code> method.</p>
<h2 id="imc-process">IMC Process<a class="headerlink" href="#imc-process" title="Permanent link"> </a></h2>
<p>Here, mods should process all the messages they have received from other mods and set up integrations appropriately. A mod may retrieve the messages that have been sent to it using the <code>InterModComms.getMessages()</code> method.</p>
<h2 id="other-important-events">Other Important Events<a class="headerlink" href="#other-important-events" title="Permanent link"> </a></h2>
<ul>
<li>FMLServerStartingEvent: Register Commands</li>
</ul>
</article>