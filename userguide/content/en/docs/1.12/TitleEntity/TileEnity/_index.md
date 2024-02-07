---
linkTitle: TileEntity
---

<article class="docs-entry">
<h1 id="tileentities">TileEntities<a class="headerlink" href="#tileentities" title="Permanent link"> </a></h1>
<p>Tile Entities are like simplified Entities, that are bound to a Block.
They are used to store dynamic data, execute tick based tasks and for dynamic rendering.
Some examples from vanilla Minecraft would be: handling of inventories (chests), smelting logic on furnaces, or area effects for beacons.
More advanced examples exist in mods, such as quarries, sorting machines, pipes, and displays.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p><code>TileEntities</code> aren&rsquo;t a solution for everything and they can cause lag when used wrongly.
When possible, try to avoid them.</p>
</div>
<h2 id="creating-a-tileentity">Creating a <code>TileEntity</code><a class="headerlink" href="#creating-a-tileentity" title="Permanent link"> </a></h2>
<p>In order to create a <code>TileEntity</code> you need to extend the <code>TileEntity</code> class.
It is important that your <code>TileEntity</code> has a default constructor, so that Minecraft can properly load it.
After you&rsquo;ve created your class, you need to register the <code>TileEntity</code>. For this you need to call</p>
<pre class="highlight"><code class="language-JAVA">GameRegistry#registerTileEntity(Class&lt;? extends TileEntity&gt; tileEntityClass, ResourceLocation key)</code></pre>

<p>Where the first parameter is simply your TileEntity class and the second the registry name of your TileEntity.
At this point you can either choose to do it during the <code>FMLPreInitializationEvent</code> or during the <code>RegistryEvent.Register&lt;Block&gt;</code> event,
as <code>TileEntities</code> don&rsquo;t have their own Registry Event yet.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The method to register a <code>TileEntity</code> is using a <code>String</code> instead of a <code>ResourceLocation</code> before Forge version <code>14.23.3.2694</code>
This method creates a <code>ResourceLocation</code> from the String. Be sure to use the ResourceLocation format modid:tile_entity to avoid it being called minecraft:tile_entity.</p>
</div>
<h2 id="attaching-a-tileentity-to-a-block">Attaching a <code>TileEntity</code> to a <code>Block</code><a class="headerlink" href="#attaching-a-tileentity-to-a-block" title="Permanent link"> </a></h2>
<p>To attach your new <code>TileEntity</code> to a <code>Block</code> you need to override 2 (two) methods within the Block class.
<pre class="highlight"><code class="language-JAVA">Block#hasTileEntity(IBlockstate state)

Block#createTileEntity(World world, IBlockState state)</code></pre>
Using the parameters you can choose if the block should have a <code>TileEntity</code> or not.
Usually you will return <code>true</code> in the first method and a new instance of your <code>TileEntity</code> in the second method.
<h2 id="storing-data-within-your-tileentity">Storing Data within your <code>TileEntity</code><a class="headerlink" href="#storing-data-within-your-tileentity" title="Permanent link"> </a></h2>
<p>In order to save data, override the following two methods
<pre class="highlight"><code class="language-JAVA">TileEntity#writeToNBT(NBTTagCompound nbt)

TileEntity#readFromNBT(NBTTagCompound nbt)</code></pre>
These methods are called whenever the <code>Chunk</code> containing the <code>TileEntity</code> gets loaded from/saved to NBT.
Use them to read and write to the fields in your tile entity class.
<div class="admonition note">
<p class="admonition-title">Note<p>Whenever your data changes you need to call <code>TileEntity#markDirty()</code>, otherwise the <code>Chunk</code> containing your <code>TileEntity</code> might be skipped while the world is saved.</p>

</div>
<div class="admonition important">
<p class="admonition-title">Important<p>It is important that you call the super methods!</p>

<p>The tag names <code>id</code>, <code>x</code>, <code>y</code>, <code>z</code>, <code>ForgeData</code> and <code>ForgeCaps</code> are reserved by the super methods.</p>
</div>
<h2 id="keeping-a-tileentity-through-changing-blockstates">Keeping a <code>TileEntity</code> through changing <code>BlockStates</code><a class="headerlink" href="#keeping-a-tileentity-through-changing-blockstates" title="Permanent link"> </a></h2>
<p>There might be situations in which you need to change your <code>BlockState</code>, such as with the vanilla furnace,
which changes its state from <code>lit=false</code> to <code>lit=true</code> when fuel and something smeltable is inside.
Achieving this is rather simple, by overriding following method
<pre class="highlight"><code class="language-JAVA">TileEntity#shouldRefresh(World world, BlockPos pos, IBlockState oldState, IBlockState newSate)</code></pre>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>You should actually check the <code>BlockStates</code> and not just return <code>false</code> in order to prevent unwanted behavior and bugs. Especially as this method is also called when your <code>Block(State)</code> is replaced by another one, <code>Air</code> for example.</p>
</div>
<h2 id="ticking-tileentities">Ticking <code>TileEntities</code><a class="headerlink" href="#ticking-tileentities" title="Permanent link"> </a></h2>
<p>If you need a ticking <code>TileEntity</code>, for example to keep track of the progress during a smelting process, you need to add the <code>net.minecraft.util.ITickable</code> interface to your <code>TileEntity</code>.
Now you can implement all your calculations within
<pre class="highlight"><code class="language-JAVA">ITickable#update()</code></pre>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This method is called each tick, therefore you should avoid having complicated calculations in here.
If possible, you should make more complex calculations just every X ticks.
(The amount of ticks in a second may be lower then 20 (twenty) but won&rsquo;t be higher)</p>
</div>
<h2 id="synchronizing-the-data-to-the-client">Synchronizing the Data to the Client<a class="headerlink" href="#synchronizing-the-data-to-the-client" title="Permanent link"> </a></h2>
<p>There are 3 (three) ways of syncing data to the client.
Synchronizing on chunk load, synchronizing on block updates and synchronizing with a custom network message.</p>
<h3 id="synchronizing-on-chunk-load">Synchronizing on chunk load<a class="headerlink" href="#synchronizing-on-chunk-load" title="Permanent link"> </a></h3>
<p>For this you need to override
<pre class="highlight"><code class="language-JAVA">TileEntity#getUpdateTag()

TileEntity#handleUpdateTag(NBTTagCompound nbt)</code></pre>
Again, this is pretty simple, the first method collects the data that should be send to the client,
while the second one processes that data. If your <code>TileEntity</code> doesn&rsquo;t contain much data you might be able to use the methods out of the <code>Storing Data within your TileEntity</code> section.
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Synchronizing excessive/useless data for TileEntities can lead to network congestion. You should optimize your network usage by sending only the information the client needs when the client needs it. For instance, it is more often that not unnecessary to send the inventory of a tile entity in the update tag, as this can be synchronized via its GUI.</p>
</div>
<h3 id="synchronizing-on-block-update">Synchronizing on block update<a class="headerlink" href="#synchronizing-on-block-update" title="Permanent link"> </a></h3>
<p>This method is a bit more complicated, but again you just need to override 2 methods.
Here is a tiny example implementation of it
<pre class="highlight"><code class="language-JAVA">@Override
public SPacketUpdateTileEntity getUpdatePacket(){
    NBTTagCompound nbtTag = new NBTTagCompound();
    //Write your data into the nbtTag
    return new SPacketUpdateTileEntity(getPos(), 1, nbtTag);
}

@Override
public void onDataPacket(NetworkManager net, SPacketUpdateTileEntity pkt){
    NBTTagCompound tag = pkt.getNbtCompound();
    //Handle your Data
}</code></pre>
The Constructor of <code>SPacketUpdateTileEntity</code> takes:
<ul>
<li>The position of your <code>TileEntity</code>.</li>
<li>An ID, though it isn&rsquo;t really used besides by Vanilla, therefore you can just put a 1 in there.</li>
<li>An <code>NBTTagCompound</code> which should contain your data.</li>
</ul>
<p>Additionally to this you now need to cause a &ldquo;BlockUpdate&rdquo; on the Client.
<pre class="highlight"><code class="language-JAVA">World#notifyBlockUpdate(BlockPos pos, IBlockState oldState, IBlockState newState, int flags)</code></pre>
The <code>pos</code> should be your TileEntitiy&rsquo;s position. For <code>oldState</code> and <code>newState</code> you can pass the current BlockState at that position.
The <code>flags</code>are a bitmask and should contain 2, which will sync the changes to the client.
<h3 id="synchronizing-using-a-custom-network-message">Synchronizing using a custom network message<a class="headerlink" href="#synchronizing-using-a-custom-network-message" title="Permanent link"> </a></h3>
<p>This way of synchronizing is probably the most complicated one, but is usually also the most optimized one,
as you can make sure that only the data you need to be synchronized is actually synchronized.
You should first check out the <a href="../../networking/index.htm"><code>Networking</code></a> section and especially <a href="../../networking/simpleimpl/index.htm"><code>SimpleImpl</code></a> before attempting this.
Once you&rsquo;ve created your custom network message, you can send it to all users that have the <code>TileEntity</code> loaded with:
<pre class="highlight"><code class="language-JAVA">SimpleNetworkWrapper#sendToAllTracking(IMessage, NetworkRegistry.TargetPoint)</code></pre>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>It is important that you do safety checks, the TileEntity might already be destroyed/replaced when the message arrives at the player!
You should also check if the chunk is loaded (<code>World#isBlockLoaded(BlockPos)</code>)</p>
</div>
</article>