---
linkTitle: SimpleIMPL
---

<article class="docs-entry">
<h1 id="simpleimpl">SimpleImpl<a class="headerlink" href="#simpleimpl" title="Permanent link"> </a></h1>
<p>SimpleImpl is the name given to the packet system that revolves around the <code>SimpleChannel</code> class. Using this system is by far the easiest way to send custom data between clients and the server.</p>
<h2 id="getting-started">Getting Started<a class="headerlink" href="#getting-started" title="Permanent link"> </a></h2>
<p>First you need to create your <code>SimpleChannel</code> object. We recommend that you do this in a separate class, possibly something like <code>ModidPacketHandler</code>. Create your <code>SimpleChannel</code> as a static field in this class, like so:</p>
<pre class="highlight"><code class="language-java">private static final String PROTOCOL_VERSION = "1";
public static final SimpleChannel INSTANCE = NetworkRegistry.newSimpleChannel(
    new ResourceLocation("mymodid", "main"),
    () -&gt; PROTOCOL_VERSION,
    PROTOCOL_VERSION::equals,
    PROTOCOL_VERSION::equals
);</code></pre>

<p>The first argument is a name for the channel. The second argument is a <code>Supplier&lt;String&gt;</code> returning the current network protocol version. The third and fourth arguments respectively are <code>Predicate&lt;String&gt;</code> checking whether an incoming connection protocol version is network-compatible with the client or server, respectively.
Here, we simply compare with the <code>PROTOCOL_VERSION</code> field directly, meaning that the client and server <code>PROTOCOL_VERSION</code>s must always match or FML will deny login.</p>
<h2 id="registering-packets">Registering Packets<a class="headerlink" href="#registering-packets" title="Permanent link"> </a></h2>
<p>Next, we must declare the types of messages that we would like to send and receive. This is done using the <code>INSTANCE.registerMessage</code> method, which takes 5 parameters.</p>
<ul>
<li>The first parameter is the discriminator for the packet. This is a per-channel unique ID for the packet. We recommend you use a local variable to hold the ID, and then call registerMessage using <code>id++</code>. This will guarantee 100% unique IDs.</li>
<li>The second parameter is the actual packet class <code>MSG</code>.</li>
<li>The third parameter is a <code>BiConsumer&lt;MSG, PacketBuffer&gt;</code> responsible for encoding the message into the provided <code>PacketBuffer</code></li>
<li>The fourth parameter is a <code>Function&lt;PacketBuffer, MSG&gt;</code> responsible for decoding the message from the provided <code>PacketBuffer</code></li>
<li>The final parameter is a <code>BiConsumer&lt;MSG, Supplier&lt;NetworkEvent.Context&gt;&gt;</code> responsible for handling the message itself</li>
</ul>
<p>The last three parameters can be method references to either static or instance methods in Java. Remember that an instance method <code>MSG.encode(PacketBuffer)</code> still satisfies <code>BiConsumer&lt;MSG, PacketBuffer&gt;</code>, the <code>MSG</code> simply becomes the implicit first argument.</p>
<h2 id="handling-packets">Handling Packets<a class="headerlink" href="#handling-packets" title="Permanent link"> </a></h2>
<p>There are a couple things to highlight in a packet handler. A packet handler has both the message object and the network context available to it. The context allows access to the player that sent the packet (if on the server), and a way to enqueue threadsafe work.</p>
<pre class="highlight"><code class="language-Java">public static void handle(MyMessage msg, Supplier&lt;NetworkEvent.Context&gt; ctx) {
    ctx.get().enqueueWork(() -&gt; {
        // Work that needs to be threadsafe (most work)
        EntityPlayerMP sender = ctx.get().getSender(); // the client that sent this packet
        // do stuff
    });
    ctx.get().setPacketHandled(true);
}</code></pre>

<p>Note the presence of <code>setPacketHandled</code>, which used to tell the network system that the packet has successfully completed handling.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>As of Minecraft 1.8 packets are by default handled on the network thread.</p>
<p>That means that your handler can <em>not</em> interact with most game objects directly.
Forge provides a convenient way to make your code execute on the main thread instead using <code>IThreadListener.addScheduledTask</code>.
Simply call <code>ctx.get().enqueueWork(Runnable)</code>, which will call the given <code>Runnable</code> on the main thread at the next opportunity.</p>
</div>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>Be defensive when handling packets on the server. A client could attempt to exploit the packet handling by sending unexpected data.</p>
<p>A common problem is vulnerability to <strong>arbitrary chunk generation</strong>. This typically happens when the server is trusting a block position sent by a client to access blocks and tile entities. When accessing blocks and tile entities in unloaded areas of the world, the server will either generate or load this area from disk, then promply write it to disk. This can be exploited to cause <strong>catastrophic damage</strong> to a server&rsquo;s performance and storage space without leaving a trace.</p>
<p>To avoid this problem, a general rule of thumb is to only access blocks and tile entities if <code>world.isBlockLoaded(pos)</code> is true.</p>
</div>
<h2 id="sending-packets">Sending Packets<a class="headerlink" href="#sending-packets" title="Permanent link"> </a></h2>
<h3 id="sending-to-the-server">Sending to the Server<a class="headerlink" href="#sending-to-the-server" title="Permanent link"> </a></h3>
<p>There is but one way to send a packet to the server. This is because there is only ever <em>one</em> server the client can be connected to at once. To do so, we must again use that <code>SimpleChannel</code> that was defined earlier. Simply call <code>INSTANCE.sendToServer(new MyMessage())</code>. The message will be sent to the handler for its type, if one exists.</p>
<h3 id="sending-to-clients">Sending to Clients<a class="headerlink" href="#sending-to-clients" title="Permanent link"> </a></h3>
<p>Packets can be sent directly to a client using the <code>SimpleChannel</code>: <code>HANDLER.sendTo(MSG, entityPlayerMP.connection.getNetworkManager(), NetworkDirection.PLAY_TO_CLIENT)</code>. However, this can be quite inconvenient. Forge has some convenience functions that can be used:</p>
<pre class="highlight"><code class="language-Java">// Sending to one player
INSTANCE.send(PacketDistributor.PLAYER.with(playerMP), new MyMessage());

// Send to all players tracking this chunk
INSTANCE.send(PacketDistributor.TRACKING_CHUNK.with(chunk), new MyMessage());

// Sending to all connected players
INSTANCE.send(PacketDistributor.ALL.noArg(), new MyMessage());</code></pre>

<p>There are additional <code>PacketDistributor</code> types available, check the documentation on the <code>PacketDistributor</code> class for more details.</p>
</article>