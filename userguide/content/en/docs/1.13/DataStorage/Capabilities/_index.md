---
linkTitle: Capabilities
---

<article class="docs-entry">
<h1 id="the-capability-system">The Capability System<a class="headerlink" href="#the-capability-system" title="Permanent link"> </a></h1>
<p>Capabilities allow exposing features in a dynamic and flexible way, without having to resort to directly implementing many interfaces.</p>
<p>In general terms, each capability provides a feature in the form of an interface, alongside with a default implementation which can be requested, and a storage handler for at least this default implementation. The storage handler can support other implementations, but this is up to the capability implementor, so look it up in their documentation before trying to use the default storage with non-default implementations.</p>
<p>Forge adds capability support to TileEntities, Entities, ItemStacks, Worlds and Chunks, which can be exposed either by attaching them through an event or by overriding the capability methods in your own implementations of the objects. This will be explained in more detail in the following sections.</p>
<h2 id="forge-provided-capabilities">Forge-provided Capabilities<a class="headerlink" href="#forge-provided-capabilities" title="Permanent link"> </a></h2>
<p>Forge provides three capabilities: IItemHandler, IFluidHandler and IEnergyStorage</p>
<p>IItemHandler exposes an interface for handling inventory slots. It can be applied to TileEntities (chests, machines, etc.), Entities (extra player slots, mob/creature inventories/bags), or ItemStacks (portable backpacks and such). It replaces the old <code>IInventory</code> and <code>ISidedInventory</code> with an automation-friendly system.</p>
<p>IFluidHandler exposes an interface for handling fluid inventories. It can also be applied to TileEntities Entities, or ItemStacks. It replaces the old <code>IFluidHandler</code> with a more consistent and automation-friendly system.</p>
<p>IEnergyStorage exposes an interface for handling energy containers. It can be applied to TileEntities, Entities or ItemStacks. It is based on the RedstoneFlux API by TeamCoFH.</p>
<h2 id="using-an-existing-capability">Using an Existing Capability<a class="headerlink" href="#using-an-existing-capability" title="Permanent link"> </a></h2>
<p>As mentioned earlier, TileEntities, Entities, and ItemStacks implement the capability provider feature, through the <code>ICapabilityProvider</code> interface. This interface adds two methods, <code>hasCapability</code> and <code>getCapability</code>, which can be used to query the capabilities present in the objects.</p>
<p>In order to obtain a capability, you will need to refer it by its unique instance. In the case of the Item Handler, this capability is primarily stored in <code>CapabilityItemHandler.ITEM_HANDLER_CAPABILITY</code>, but it is possible to get other instance references by using the <code>@CapabilityInject</code> annotation.</p>
<pre class="highlight"><code class="language-Java">@CapabilityInject(IItemHandler.class)
static Capability&lt;IItemHandler&gt; ITEM_HANDLER_CAPABILITY = null;</code></pre>

<p>This annotation can be applied to fields and methods. When applied to a field, it will assign the instance of the capability (the same one gets assigned to all fields) upon registration of the capability, and left to the existing value (<code>null</code>), if the capability was never registered. Because local static field accesses are fast, it is a good idea to keep your own local copy of the reference for objects that work with capabilities. This annotation can also be used on a method, in order to get notified when a capability is registered, so that certain features can be enabled conditionally.</p>
<p>Both the <code>hasCapability</code> and <code>getCapability</code> methods have a second parameter, of type EnumFacing, which can be used in the to request the specific instance for that one face. If passed <code>null</code>, it can be assumed that the request comes either from within the block, or from some place where the side has no meaning, such as a different dimension. In this case a general capability instance that does not care about sides will be requested instead. The return type of <code>getCapability</code> will correspond to the type declared in the capability passed to the method. For the Item Handler capability, this is indeed <code>IItemHandler</code>.</p>
<h2 id="exposing-a-capability">Exposing a Capability<a class="headerlink" href="#exposing-a-capability" title="Permanent link"> </a></h2>
<p>In order to expose a capability, you will first need an instance of the underlying capability type. Note that you should assign a separate instance to each object that keeps the capability, since the capability will most probably be tied to the containing object.</p>
<p>There&rsquo;s two ways to obtain such an instance, through the Capability itself, or by explicitly instantiating an implementation of it. The first method is designed to use a default implementation, if those default values are useful for you. In the case of the Item Handler capability, the default implementation will expose a single slot inventory, which is most probably not what you want.</p>
<p>The second method can be used to provide custom implementations. In the case of <code>IItemHandler</code>, the default implementation uses the <code>ItemStackHandler</code> class, which has an optional argument in the constructor, to specify a number of slots. However, relying on the existence of these default implementations should be avoided, as the purpose of the capability system is to prevent loading errors in contexts where the capability is not present, so instantiation should be protected behind a check testing if the capability has been registered (see the remarks about <code>@CapabilityInject</code> in the previous section).</p>
<p>Once you have your own instance of the capability interface, you will want to notify users of the capability system that you expose this capability. This is done by overriding the <code>hasCapability</code> method, and comparing the instance with the capability you are exposing. If your machine has different slots based on which side is being queried, you can test this with the <code>facing</code> parameter. For Entities and ItemStacks, this parameter can be ignored, but it is still possible to have side as a context, such as different armor slots on a player (top side =&gt; head slot?), or about the surrounding blocks in the inventory (west =&gt; slot on the left?). Don&rsquo;t forget to fall back to <code>super</code>, otherwise the attached capabilities will stop working.</p>
<pre class="highlight"><code class="language-Java">@Override
public boolean hasCapability(Capability&lt;?&gt; capability, EnumFacing facing) {
  if (capability == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
    return true;
  }
  return super.hasCapability(capability, facing);
}</code></pre>

<p>Similarly, you will want to provide the interface reference to your capability instance, when requested. Again, don&rsquo;t forget to fall back to <code>super</code>.</p>
<pre class="highlight"><code class="language-Java">@Override
public &lt;T&gt; T getCapability(Capability&lt;T&gt; capability, EnumFacing facing) {
  if (capability == CapabilityItemHandler.ITEM_HANDLER_CAPABILITY) {
    return (T) inventory;
  }
  return super.getCapability(capability, facing);
}</code></pre>

<p>It is strongly suggested that direct checks in code are used to test for capabilities instead of attempting to rely on maps or other data structures, since capability tests can be done by many objects every tick, and they need to be as fast as possible in order to avoid slowing down the game.</p>
<h2 id="attaching-capabilities">Attaching Capabilities<a class="headerlink" href="#attaching-capabilities" title="Permanent link"> </a></h2>
<p>As mentioned, attaching capabilities to entities and itemstacks can be done using <code>AttachCapabilitiesEvent</code>. The same event is used for all objects that can provide capabilities. <code>AttachCapabilitiesEvent</code> has 5 valid generic types providing the following events:</p>
<ul>
<li><code>AttachCapabilitiesEvent&lt;Entity&gt;</code>: Fires only for entities.</li>
<li><code>AttachCapabilitiesEvent&lt;TileEntity&gt;</code>: Fires only for tile entities.</li>
<li><code>AttachCapabilitiesEvent&lt;ItemStack&gt;</code>: Fires only for item stacks.</li>
<li><code>AttachCapabilitiesEvent&lt;World&gt;</code>: Fires only for worlds.</li>
<li><code>AttachCapabilitiesEvent&lt;Chunk&gt;</code>: Fires only for chunks.</li>
</ul>
<p>The generic type cannot be more specific than the above types. For example: If you want to attach capabilities to <code>EntityPlayer</code>, you have to subscribe to the <code>AttachCapabilitiesEvent&lt;Entity&gt;</code>, and then determine that the provided object is an <code>EntityPlayer</code> before attaching the capability.</p>
<p>In all cases, the event has a method <code>addCapability</code>, which can be used to attach capabilities to the target object. Instead of adding capabilities themselves to the list, you add capability providers, which have the chance to return capabilities only from certain sides. While the provider only needs to implement <code>ICapabilityProvider</code>, if the capability needs to store data persistently it is possible to implement <code>ICapabilitySerializable&lt;T extends NBTBase&gt;</code> which, on top of returning the capabilities, will allow providing NBT save/load functions.</p>
<p>For information on how to implement <code>ICapabilityProvider</code>, refer to the <a href="#exposing-a-capability">Exposing a Capability</a> section.</p>
<h2 id="creating-your-own-capability">Creating Your Own Capability<a class="headerlink" href="#creating-your-own-capability" title="Permanent link"> </a></h2>
<p>In general terms, a capability is declared and registered through a single method call to <code>CapabilityManager.INSTANCE.register()</code>. One possibility is to define a static <code>register()</code> method inside a dedicated class for the capability, but this is not required by the capability system. For the purpose of this documentation we will be describing each part as a separate named class, although anonymous classes are an option.</p>
<pre class="highlight"><code>CapabilityManager.INSTANCE.register(capability interface class, storage, default implementation factory);</code></pre>

<p>The first parameter to this method, is the type that describes the capability feature. In our example, this will be <code>IExampleCapability.class</code>.</p>
<p>The second parameter is an instance of a class that implements <code>Capability.IStorage&lt;T&gt;</code>, where T is the same class we specified in the first parameter. This storage class will help manage saving and loading for the default implementation, and it can, optionally, also support other implementations.</p>
<pre class="highlight"><code class="language-Java">private static class Storage
    implements Capability.IStorage&lt;IExampleCapability&gt; {

  @Override
  public NBTBase writeNBT(Capability&lt;IExampleCapability&gt; capability, IExampleCapability instance, EnumFacing side) {
    // return an NBT tag
  }

  @Override
  public void readNBT(Capability&lt;IExampleCapability&gt; capability, IExampleCapability instance, EnumFacing side, NBTBase nbt) {
    // load from the NBT tag
  }
}</code></pre>

<p>The last parameter is a callable factory that will return new instances of the default implementation.</p>
<pre class="highlight"><code class="language-Java">private static class Factory implements Callable&lt;IExampleCapability&gt; {

  @Override
  public IExampleCapability call() throws Exception {
    return new Implementation();
  }
}</code></pre>

<p>Finally, we will need the default implementation itself, to be able to instantiate it in the factory. Designing this class is up to you, but it should at least provide a basic skeleton that people can use to test the capability, if it&rsquo;s not a fully usable implementation on itself.</p>
<h2 id="persisting-chunk-and-tileentity-capabilities">Persisting Chunk and TileEntity capabilities<a class="headerlink" href="#persisting-chunk-and-tileentity-capabilities" title="Permanent link"> </a></h2>
<p>Unlike Worlds, Entities and ItemStacks, Chunks and TileEntities are only written to disk when they have been marked as dirty. A capability implementation with persistent state for a Chunk or a TileEntity should therefore ensure that whenever its state changes, its owner is marked as dirty.</p>
<p><code>ItemStackHandler</code>, commonly used for inventories in TileEntities, has an overridable method <code>void onContentsChanged(int slot)</code> designed to be used to mark the TileEntity as dirty.</p>
<pre class="highlight"><code class="language-Java">public class MyTileEntity extends TileEntity {

  private final IItemHandler inventory = new ItemStackHandler(...) {
    @Override
    protected void onContentsChanged(int slot) {
      super.onContentsChanged(slot);
      markDirty();
    }
  }

  ...
}</code></pre>

<h2 id="synchronizing-data-with-clients">Synchronizing Data with Clients<a class="headerlink" href="#synchronizing-data-with-clients" title="Permanent link"> </a></h2>
<p>By default, Capability data is not sent to clients. In order to change this, the mods have to manage their own synchronization code using packets.</p>
<p>There are three different situation in which you may want to send synchronization packets, all of them optional:</p>
<ol>
<li>When the entity spawns in the world, or the block is placed, you may want to share the initialization-assigned values with the clients.</li>
<li>When the stored data changes, you may want to notify some or all of the watching clients.</li>
<li>When a new client starts viewing the entity or block, you may want to notify it of the existing data.</li>
</ol>
<p>Refer to the <a href="../../networking/index.htm">Networking</a> page for more information on implementing network packets.</p>
<h2 id="persisting-across-player-deaths">Persisting across Player Deaths<a class="headerlink" href="#persisting-across-player-deaths" title="Permanent link"> </a></h2>
<p>By default, the capability data does not persist on death. In order to change this, the data has to be manually copied when the player entity is cloned during the respawn process.</p>
<p>This can be done by handling the <code>PlayerEvent.Clone</code> event, reading the data from the original entity, and assigning it to the new entity. In this event, the <code>wasDead</code> field can be used to distinguish between respawning after death, and returning from the End. This is important because the data will already exist when returning from the End, so care has to be taken to not duplicate values in this case.</p>
<h2 id="migrating-from-iextendedentityproperties">Migrating from IExtendedEntityProperties<a class="headerlink" href="#migrating-from-iextendedentityproperties" title="Permanent link"> </a></h2>
<p>Although the Capability system can do everything IEEPs (IExtendedEntityProperties) did and more, the two concepts don&rsquo;t fully match 1:1. In this section, I will explain how to convert existing IEEPs into Capabilities.</p>
<p>This is a quick list of IEEP concepts and their Capability equivalent:</p>
<ul>
<li>Property name/id (<code>String</code>): Capability key (<code>ResourceLocation</code>)</li>
<li>Registration (<code>EntityConstructing</code>): Attaching (<code>AttachCapabilitiesEvent&lt;Entity&gt;</code>), the real registration of the Capability happens during pre-init.</li>
<li>NBT read/write methods: Does not happen automatically. Attach an <code>ICapabilitySerializable</code> in the event, and run the read/write methods from the <code>serializeNBT</code>/<code>deserializeNBT</code>.</li>
</ul>
<p>Features you probably will not need (if the IEEP was for internal use only):</p>
<ul>
<li>The Capability system provides a default implementation concept, meant to simplify usage by third party consumers, but it doesn&rsquo;t really make much sense for an internal Capability designed to replace an IEEP. You can safely return <code>null</code> from the factory if the capability is be for internal use only.</li>
<li>The Capability system provides an <code>IStorage</code> system that can be used to read/write data from those default implementations, if you choose not to provide default implementations, then the IStorage system will never get called, and can be left blank.</li>
</ul>
<p>The following steps assume you have read the rest of the document, and you understand the concepts of the capability system.</p>
<p>Quick conversion guide:</p>
<ol>
<li>Convert the IEEP key/id string into a <code>ResourceLocation</code> (which will use your MODID as a namespace).</li>
<li>In your handler class (not the class that implements your capability interface), create a field that will hold the Capability instance.</li>
<li>Change the <code>EntityConstructing</code> event to <code>AttachCapabilitiesEvent</code>, and instead of querying the IEEP, you will want to attach an <code>ICapabilityProvider</code> (probably <code>ICapabilitySerializable</code>, which allows saving/loading from NBT).</li>
<li>Create a registration method if you don&rsquo;t have one (you may have one where you registered your IEEP&rsquo;s event handlers) and in it, run the capability registration function.</li>
</ol>
</article>