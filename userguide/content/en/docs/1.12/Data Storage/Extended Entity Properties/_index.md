---
linkTitle: Extended Entity Properties
---

<article class="docs-entry">
<h1 id="extended-entity-properties">Extended Entity Properties<a class="headerlink" href="#extended-entity-properties" title="Permanent link"> </a></h1>
<p>Extended entity properties allow attaching data to entities.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>This system has been <em>deprecated</em> in favor of the <a href="../capabilities/index.htm">Capability</a> system.</p>
</div>
<h2 id="declaration-and-registration">Declaration and Registration<a class="headerlink" href="#declaration-and-registration" title="Permanent link"> </a></h2>
<p>The basis of the EEPs is the <code>IExtendedEntityProperties</code> interface. This interface provides the basic methods required for managing the extended data:</p>
<ul>
<li><code>init</code>: Allows the implementation to have knowledge about the entity it&rsquo;s attached to, and the world this entity is loaded into.</li>
<li><code>saveNBTData</code>: Allows the implementation to store data in the save file, to be loaded when the entity gets loaded into the world.</li>
<li><code>loadNBTData</code>: Allows the implementation to read the previously saved data for this entity.</li>
</ul>
<p>An implementation will have a class implementing this interface, and instances of this class will be attached to the entities, ready to store any required data.</p>
<p>The implementation will need to make use of events in order to attach the IEEP to entities, and optionally any other required features. To help with encapsulation, you may want to create an inner class for handling those events. In the example, I created a <code>register()</code> method to help with that.</p>
<p>A basic skeleton to get started:</p>
<pre class="highlight"><code class="language-java">public class ExampleEntityProperty implements IExtendedEntityProperties {
  public static final String PROP_NAME = ExampleMod.MODID + "_ExampleEntityData";

  public static void register() {
    MinecraftForge.EVENT_BUS.register(new Handler());
  }

  // IExtendedEntityProperties methods go here

  public static class Handler {
    // Event handlers will go here
  }
}</code></pre>

<h2 id="attaching-the-implementation-to-entities">Attaching the Implementation to Entities<a class="headerlink" href="#attaching-the-implementation-to-entities" title="Permanent link"> </a></h2>
<p>In order to attach the extended property to an entity, it is done by handling the <code>EntityEvent.EntityConstructing</code> event, and if the entity is of interest, using the <code>Entity#registerExtendedProperties</code> method.</p>
<p>In order to uniquely identify your property and avoid duplication, the method takes a string parameter with an identifier for the property. A good practice is to include the modid in this string, so that it will not collide with other mods.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>If the same property identifier is added twice, Forge will append a number to it, and return this modified identifier from the <code>registerExtendedProperties</code> method. If you don&rsquo;t want that to happen, you can use <code>Entity#getExtendedProperties</code> to check if an IEEP with that name was already added.</p>
</div>
<p>In order to handle this event, you could do something like this:</p>
<pre class="highlight"><code class="language-java">@SubscribeEvent
public void entityConstruct(EntityEvent.EntityConstructing e) {
  if (e.entity instanceof EntityPlayer) {
    if (e.entity.getExtendedProperties(PROP_NAME) == null) {
      e.entity.registerExtendedProperties(PROP_NAME, new ExampleEntityProperty());
    }
  }
}</code></pre>

<h2 id="making-use-of-the-implementation">Making Use of the Implementation<a class="headerlink" href="#making-use-of-the-implementation" title="Permanent link"> </a></h2>
<p>To make use of the extended data, the instance of the IEEP implementation has to be obtained from the Entity, and because the entity could have been unloaded or may have changed dimensions, it is not safe to cache the references.</p>
<p>To obtain the IEEP reference, one would use <code>Entity#getExtendedProperties</code>, with the same property ID specified on registration. The return value, if not <code>null</code>, is the instance of <code>IExtendedEntityProperties</code> added during entity construction.</p>
<p>A good idea is to create a static <code>get</code> method in your IEEP implementation, that will automatically obtain the instance, and cast it to your implementation class. It can be as simple as:</p>
<pre class="highlight"><code class="language-java">public static ExampleEntityProperty get(Entity p) {
  return (ExampleEntityProperty) p.getExtendedProperties(PROP_NAME);
}</code></pre>

<h2 id="saving-and-loading-data-from-nbt">Saving and Loading Data from NBT<a class="headerlink" href="#saving-and-loading-data-from-nbt" title="Permanent link"> </a></h2>
<p>Forge allows all IEEPs attached to an entity to save and load themselves. However, keep in mind that the NBT tag provided in the <code>saveNBTData</code> and <code>loadNBTData</code> methods is a global tag for the entity, and may contain data for other IEEPs, along with all the data from the entity itself.</p>
<p>There are some cases where an IEEP may benefit from accessing this global data, but for the most common use cases, it is important to avoid colliding with existing data, preferably by storing the data in a nested tag, using an unique name for it (such as the name used to identify the IEEP).</p>
<p>Your code may look a bit like this:</p>
<pre class="highlight"><code class="language-java">@Override
public void saveNBTData(NBTTagCompound compound) {
  NBTTagCompound propertyData = new NBTTagCompound();

  // Write data to propertyData

  compound.setTag(PROP_NAME, propertyData);
}

@Override
public void loadNBTData(NBTTagCompound compound) {
  if(compound.hasKey(PROP_NAME, Constants.NBT.TAG_COMPOUND)) {
    NBTTagCompound propertyData = compound.getCompoundTag(PROP_NAME);

    // Read data from propertyData
  }
}</code></pre>

<h2 id="synchronizing-data-with-clients">Synchronizing Data with Clients<a class="headerlink" href="#synchronizing-data-with-clients" title="Permanent link"> </a></h2>
<p>By default, the entity data is not sent to clients. In order to change this, the mods have to manage their own synchronization code using packets.</p>
<p>There are three different situation in which you may want to send synchronization packets, all of them optional:</p>
<ol>
<li>When the entity spawns in the world, you may want to share the initialization-assigned values with the clients.</li>
<li>When the stored data changes, you may want to notify some or all of the watching clients.</li>
<li>When a new client starts viewing the entity, you may want to notify it of the existing data.</li>
</ol>
<p>Refer to the <a href="../../networking/index.htm">Networking</a> page for more information on implementing the network packets.</p>
<p>For example:</p>
<pre class="highlight"><code class="language-java">private void dataChanged() {
  if(!world.isRemote) {
    EntityTracker tracker = ((WorldServer)world).getEntityTracker();
    ExampleEntityPropertySync message = new ExampleEntityPropertySync(this);

    for (EntityPlayer entityPlayer : tracker.getTrackingPlayers(entity)) {
      ExampleMod.channel.sendTo(message, (EntityPlayerMP)entityPlayer);
    }
  }
}

private void entitySpawned() {
  dataChanged();
}

private void playerStartedTracking(EntityPlayer entityPlayer) {
  ExampleMod.channel.sendTo(new ExampleEntityPropertySync(this), (EntityPlayerMP)entityPlayer);
}</code></pre>

<p>And the corresponding event handlers:</p>
<pre class="highlight"><code class="language-java">@SubscribeEvent
public void entityJoinWorld(EntityJoinWorldEvent e) {
  ExampleEntityProperty data = ExampleEntityProperty.get(e.entity);
  if (data != null)
    data.entitySpawned();
}

@SubscribeEvent
public void playerStartedTracking(PlayerEvent.StartTracking e) {
  ExampleEntityProperty data = ExampleEntityProperty.get(e.target);
  if (data != null)
    data.playerStartedTracking(e.entityPlayer);
}</code></pre>

<h2 id="persisting-across-player-deaths">Persisting across Player Deaths<a class="headerlink" href="#persisting-across-player-deaths" title="Permanent link"> </a></h2>
<p>By default, the entity data does not persist on death. In order to change this, the data has to be manually copied when the player entity is cloned during the respawn process.</p>
<p>This can be done by handling the <code>PlayerEvent.Clone</code> event. In this event, the <code>wasDead</code> field can be used to distinguish between respawning after death, and returning from the End. This is important because the data will already exist, so care has to be taken to not duplicate values when returning from the End dimension.</p>
<pre class="highlight"><code class="language-java">@SubscribeEvent
public void onClonePlayer(PlayerEvent.Clone e) {
  if(e.wasDeath) {
    NBTTagCompound compound = new NBTTagCompound();
    ExampleEntityProperty.get(e.original).saveNBTData(compound);
    ExampleEntityProperty.get(e.entityPlayer).loadNBTData(compound);
  }
}</code></pre>
</article>