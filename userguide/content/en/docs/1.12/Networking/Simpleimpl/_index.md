---
linkTitle: SimpleIMPL
---

<article class="docs-entry">
<h1 id="simpleimpl">SimpleImpl<a class="headerlink" href="#simpleimpl" title="Permanent link"> </a></h1>
<p>SimpleImpl is the name given to the packet system that revolves around the <code>SimpleNetworkWrapper</code> class. Using this system is by far the easiest way to send custom data between clients and the server.</p>
<h2 id="getting-started">Getting Started<a class="headerlink" href="#getting-started" title="Permanent link"> </a></h2>
<p>First you need to create your <code>SimpleNetworkWrapper</code> object. We recommend that you do this in a separate class, possibly something like <code>ModidPacketHandler</code>. Create your <code>SimpleNetworkWrapper</code> as a static field in this class, like so:</p>
<pre class="highlight"><code class="language-java">public static final SimpleNetworkWrapper INSTANCE = NetworkRegistry.INSTANCE.newSimpleChannel("mymodid");</code></pre>

<p>Where <code>"mymodid"</code> is a short identifier for your packet channel, typically just your mod ID, unless that is unusually long.</p>
<h2 id="making-packets">Making Packets<a class="headerlink" href="#making-packets" title="Permanent link"> </a></h2>
<h3 id="imessage">IMessage<a class="headerlink" href="#imessage" title="Permanent link"> </a></h3>
<p>A packet is defined by using the <code>IMessage</code> interface. This interface defines 2 methods, <code>toBytes</code> and <code>fromBytes</code>. These methods, respectively, write and read the data in your packet to and from a <code>ByteBuf</code> object, which is an object used to hold a stream (array) of bytes which are sent through the network.</p>
<p>For an example, let&rsquo;s define a small packet that is designed to send a single int over the network:</p>
<pre class="highlight"><code class="language-java">public class MyMessage implements IMessage {
  // A default constructor is always required
  public MyMessage(){}

  private int toSend;
  public MyMessage(int toSend) {
    this.toSend = toSend;
  }

  @Override public void toBytes(ByteBuf buf) {
    // Writes the int into the buf
    buf.writeInt(toSend);
  }

  @Override public void fromBytes(ByteBuf buf) {
    // Reads the int back from the buf. Note that if you have multiple values, you must read in the same order you wrote.
    toSend = buf.readInt();
  }
}</code></pre>

<h3 id="imessagehandler">IMessageHandler<a class="headerlink" href="#imessagehandler" title="Permanent link"> </a></h3>
<p>Now, how can we use this packet? Well, first we must have a class that can <em>handle</em> this packet. This is created with the <code>IMessageHandler</code> interface. Say we wanted to use this integer we sent to give the player that many diamonds on the server. Let&rsquo;s make this handler:</p>
<pre class="highlight"><code class="language-java">// The params of the IMessageHandler are &lt;REQ, REPLY&gt;
// This means that the first param is the packet you are receiving, and the second is the packet you are returning.
// The returned packet can be used as a "response" from a sent packet.
public class MyMessageHandler implements IMessageHandler&lt;MyMessage, IMessage&gt; {
  // Do note that the default constructor is required, but implicitly defined in this case

  @Override public IMessage onMessage(MyMessage message, MessageContext ctx) {
    // This is the player the packet was sent to the server from
    EntityPlayerMP serverPlayer = ctx.getServerHandler().playerEntity;
    // The value that was sent
    int amount = message.toSend;
    // Execute the action on the main server thread by adding it as a scheduled task
    serverPlayer.getServerWorld().addScheduledTask(() -&gt; {
      serverPlayer.inventory.addItemStackToInventory(new ItemStack(Items.DIAMOND, amount));
    });
    // No response packet
    return null;
  }
}</code></pre>

<p>It is recommended (but not required) that for organization&rsquo;s sake, this class is an inner class to your MyMessage class. If this is done, note that the class must also be declared <code>static</code>.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>As of Minecraft 1.8 packets are by default handled on the network thread.</p>
<p>That means that your <code>IMessageHandler</code> can <em>not</em> interact with most game objects directly.
Minecraft provides a convenient way to make your code execute on the main thread instead using <code>IThreadListener.addScheduledTask</code>.</p>
<p>The way to obtain an <code>IThreadListener</code> is using either the <code>Minecraft</code> instance (client side) or a <code>WorldServer</code> instance (server side). The code above shows an example of this by getting a <code>WorldServer</code> instance from an <code>EntityPlayerMP</code>.</p>
</div>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>Be defensive when handling packets on the server. A client could attempt to exploit the packet handling by sending unexpected data.</p>
<p>A common problem is vulnerability to <strong>arbitrary chunk generation</strong>. This typically happens when the server is trusting a block position sent by a client to access blocks and tile entities. When accessing blocks and tile entities in unloaded areas of the world, the server will either generate or load this area from disk, then promply write it to disk. This can be exploited to cause <strong>catastrophic damage</strong> to a server&rsquo;s performance and storage space without leaving a trace.</p>
<p>To avoid this problem, a general rule of thumb is to only access blocks and tile entities if <code>world.isBlockLoaded(pos)</code> is true.</p>
</div>
<h2 id="registering-packets">Registering Packets<a class="headerlink" href="#registering-packets" title="Permanent link"> </a></h2>
<p>So now we have a packet, and a handler for this packet. But the <code>SimpleNetworkWrapper</code> needs one more thing to function! For it to use a packet, the packet must be registered with an <em>discriminator</em>, which is just an integer used to map packet types between server and client. To do this, call <code>INSTANCE.registerMessage(MyMessageHandler.class, MyMessage.class, 0, Side.Server);</code> where <code>INSTANCE</code> is the <code>SimpleNetworkWrapper</code> we defined earlier.</p>
<p>This is quite a complex method, so lets break it down a bit.</p>
<ul>
<li>The first parameter is <code>messageHandler</code>, which is the class that handles your packet. This class must always have a default constructor, and should have type bound REQ that matches the next argument.</li>
<li>The second parameter is <code>requestMessageType</code> which is the actual packet class. This class must also have a default constructor and match the REQ type bound of the previous param.</li>
<li>The third parameter is the discriminator for the packet. This is a per-channel unique ID for the packet. We recommend you use a static variable to hold the ID, and then call registerMessage using <code>id++</code>. This will guarantee 100% unique IDs.</li>
<li>The fourth and final parameter is the side that your packet will be <strong><em>received</em></strong> on. If you are planning to send the packet to both sides, it must be registered twice, once to each side. Discriminators can be the same between sides, but are not required to be.</li>
</ul>
<h2 id="using-packets">Using Packets<a class="headerlink" href="#using-packets" title="Permanent link"> </a></h2>
<p>When sending packets, make sure that there is a handler registered <em>on the receiving side</em> for said packet. If there is not, the packet will be sent across the network and then thrown away, resulting in a &ldquo;leaked&rdquo; packet. This is harmless other than needless network usage, but should still be fixed.</p>
<h3 id="sending-to-the-server">Sending to the Server<a class="headerlink" href="#sending-to-the-server" title="Permanent link"> </a></h3>
<p>There is but one way to send a packet to the server. This is because there is only ever <em>one</em> server, and only <em>one</em> way to send to that server, of course. To do so, we must again use that <code>SimpleNetworkWrapper</code> that was defined earlier. Simply call <code>INSTANCE.sendToServer(new MyMessage(toSend))</code>. The message will be sent to the <code>Side.SERVER</code> <code>IMessageHandler</code> for its type, if one exists.</p>
<h3 id="sending-to-clients">Sending to Clients<a class="headerlink" href="#sending-to-clients" title="Permanent link"> </a></h3>
<p>There are four different methods of sending packets to clients:</p>
<ol>
<li><code>sendToAll</code> - Calling <code>INSTANCE.sendToAll</code> will send the packet once to every single player on the current server, no matter what location or dimension they are in.</li>
<li><code>sendToDimension</code> - <code>INSTANCE.sendToDimension</code> takes two arguments, an <code>IMessage</code> and an integer. The integer is the dimension ID to send to, which can be gotten with <code>world.provider.getDimension()</code>. The packet will be sent to all players currently in the given dimension.</li>
<li><code>sendToAllAround</code> - <code>INSTANCE.sendToAllAround</code> requires an <code>IMessage</code> and a <code>NetworkRegistry.TargetPoint</code> object. All players within the <code>TargetPoint</code> will have the packet sent to them. A TargetPoint requires a dimension (see #2), x/y/z coordinates, and a range. It represents a sphere with radius of range, centered at (x, y, z) in a world.</li>
<li><code>sendTo</code> - <code>INSTANCE.sendTo</code> is is the option for sending packets to a single client. This requires an <code>IMessage</code> and an <code>EntityPlayerMP</code> to which to send the packet. Note that though this is not the more generic <code>EntityPlayer</code>, as long as you are on the server you can safely cast any <code>EntityPlayer</code> to <code>EntityPlayerMP</code>.</li>
<li><code>sendToAllTracking</code> - Last but not least, there is also <code>INSTANCE.sendToAllTracking</code> which sends packets to all players on the server who are &ldquo;tracking&rdquo; the target. There are two variants of <code>sendToAllTracking</code>: one accepts a <code>TargetPoint</code>, and another one accepts an <code>Entity</code>. For the former, all clients that has the block located at <code>TargetPoint</code> loaded will receive the packet; for the latter, all clients that are within the tracking range of supplied <code>Entity</code> will receive the packet. Do note that <code>EntityPlayer</code>s will not track themselves, so this cannot replace the use of <code>sendTo</code>; if the <code>EntityPlayer</code> that is used as target also requires syncing, you need to call <code>sendTo</code> on that before calling the Entity version of <code>sendToAllTracking</code>.</li>
</ol>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The two variants of <code>sendToAllTracking</code> are not interchangeable - for the version that accepts <code>TargetPoint</code>, <code>TargetPoint.range</code> field is ignored; for the version that accepts <code>Entity</code>, the tracking range of said <code>Entity</code> is respected, and the actual values vary on different <code>Entity</code>. As such, one cannot use one of them to emulate the effect of the other. You should choose the one that fits your actual situation best.</p>
</div>
</article>