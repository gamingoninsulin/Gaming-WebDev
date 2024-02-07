---
linkTitle: Entities
---

<article class="docs-entry">
<h1 id="entities">Entities<a class="headerlink" href="#entities" title="Permanent link"> </a></h1>
<p>In addition to regular network messages, there are various other systems provided to handle synchronizing entity data.</p>
<h2 id="spawn-data">Spawn Data<a class="headerlink" href="#spawn-data" title="Permanent link"> </a></h2>
<p>In general, the spawning of modded entities is handled seperately, by Forge.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This means that simply extending a vanilla entity class may not inherit all its behaviour here. You may need to implement certain vanilla behaviours yourself.</p>
</div>
<p>You can add extra data to the spawn packet Forge sends by implementing the following interfaces.</p>
<h3 id="ientityadditionalspawndata">IEntityAdditionalSpawnData<a class="headerlink" href="#ientityadditionalspawndata" title="Permanent link"> </a></h3>
<p>If your entity has data that is needed on the client, but doesn&rsquo;t change over time, then it can be added to the entity spawn packet using this interface. <code>writeSpawnData()</code> and <code>readSpawnData()</code> control how the data should be en/decoded to/from the network buffer, in a similar fashion to the <code>toBytes()</code>/<code>fromBytes()</code> methods in <code>IMessage</code>.</p>
<h3 id="ithrowableentity">IThrowableEntity<a class="headerlink" href="#ithrowableentity" title="Permanent link"> </a></h3>
<p>This is for &ldquo;projectile&rdquo; type entities. Implementing this interface will cause the ID of the &ldquo;source&rdquo; entity, as well as the initial velocity, to be sent to the client with the spawn packet.</p>
<h2 id="dynamic-data">Dynamic Data<a class="headerlink" href="#dynamic-data" title="Permanent link"> </a></h2>
<h3 id="data-parameters">Data Parameters<a class="headerlink" href="#data-parameters" title="Permanent link"> </a></h3>
<p>This is the main vanilla system for synchronizing entity data from the server to the client. As such, a number of vanilla examples are available to refer to.</p>
<p>Firstly you need a <code>DataParameter&lt;T&gt;</code> for the data you wish to keep synchronized. This should be stored as a static final field in your entity class, obtained by calling <code>EntityDataManager.createKey()</code> and passing the entity class and a serializer for that type of data. The available serializer implementations can be found as static constants within the <code>DataSerializers</code> class.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>You should <strong>only</strong> create data parameters for your own entities, <em>within that entity&rsquo;s class</em>.
Adding parameters to entities you do not control can cause the IDs used to send that data over the network to become desynchronized, causing difficult to debug crashes.</p>
</div>
<p>Then, override <code>entityInit()</code> and call <code>this.dataManager.register()</code> for each of your data parameters, passing the parameter and an initial value to use. Remember to always call <code>super.entityInit()</code> first!</p>
<p>You can then get and set these values via your entity&rsquo;s <code>dataManager</code> instance. Changes made will be synchronized to the client automatically.</p>
</article>