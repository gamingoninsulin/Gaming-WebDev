---
linkTitle: Implementing
---

<article class="docs-entry">
<h1 id="using-the-api">Using the API<a class="headerlink" href="#using-the-api" title="Permanent link"> </a></h1>
<p>Depending on what you want to animate with the API, code-side implementation is a bit different.
Documentation on the ASM API itself (for controlling the animation) is found on the <a href="../asm/index.htm">ASM</a> page because it is independent of what
you are animating.</p>
<h2 id="blocks">Blocks<a class="headerlink" href="#blocks" title="Permanent link"> </a></h2>
<p>Animations for blocks are done with the <code>AnimationTESR</code>, which is a <code>FastTESR</code>. Because of this, having a <code>TileEntity</code> for your block
is necessary. Your <code>TileEntity</code> must provide the <code>ANIMATION_CAPABILITY</code>, which is received by calling its <code>.cast</code> method with your
ASM. Your block must also render in the <code>ENTITYBLOCK_ANIMATED</code> render layer if you do not provide a <code>StaticProperty</code> in the block&rsquo;s blockstate.</p>
<p>The <code>StaticProperty</code> is a property you can add to your block&rsquo;s blockstate by adding <code>Properties.StaticProperty</code> to the list of your block&rsquo;s properties inside
of <code>createBlockState()</code>. When rendering the block, the <code>AnimationTESR</code> checks if the property&rsquo;s value is true; if so, block rendering continues as normal. Otherwise
the <code>AnimationTESR</code> animates the block model assigned to the <code>static=false</code> variant in the blockstate json. All parts of the model that can be static should probably
be rendered in the static state, as that is its purpose.</p>
<p>The <code>handleEvents()</code> callback is located <em>in</em> the <code>AnimationTESR</code>, so you have to either subclass or overload it inline when you register the tileentity.</p>
<p>Here&rsquo;s an example of registering the TESR:</p>
<pre class="highlight"><code class="language-java">ClientRegistry.bindTileEntitySpecialRenderer(Chest.class, new AnimationTESR&lt;Chest&gt;()
{
    @Override
    public void handleEvents(Chest chest, float time, Iterable&lt;Event&gt; pastEvents)
    {
        chest.handleEvents(time, pastEvents);
    }
}); </code></pre>

<p>In this example, we&rsquo;ve overridden the <code>handleEvents()</code> callback when we registered the TESR because the implementation is simple, but you could easily subclass
AnimationTESR to achieve the same effect. The <code>handleEvents()</code> callback for blocks takes two arguments: the tile entity being rendered, and an iterable of the events.
The call to <code>chest.handleEvents()</code> calls a method located in the fictional <code>Chest</code> TileEntity, as the ASM is not accessible inside of the <code>handleEvents()</code> method.</p>
<h2 id="items">Items<a class="headerlink" href="#items" title="Permanent link"> </a></h2>
<p>Animations for items are done entirely using the capability system. Your item must provide the <code>ANIMATION_CAPABILITY</code> through an <code>ICapabilityProvider</code>. You can create
an instance of this capability using its <code>.cast</code> method with your ASM, which is usually stored on the <code>ICapabilityProvider</code> object itself. An example of this is below:</p>
<pre class="highlight"><code class="language-java">private static class ItemAnimationHolder implements ICapabilityProvider
{
    private final VariableValue cycleLength = new VariableValue(4);

    private final IAnimationStateMachine asm = proxy.load(new ResourceLocation(MODID.toLowerCase(), "asms/block/engine.json"), ImmutableMap.&lt;String, ITimeValue&gt;of(
        "cycle_length", cycleLength
    ));

    @Override
    public boolean hasCapability(@Nonnull Capability&lt;?&gt; capability, @Nullable EnumFacing facing)
    {
        return capability == CapabilityAnimation.ANIMATION_CAPABILITY;
    }

    @Override
    @Nullable
    public &lt;T&gt; T getCapability(@Nonnull Capability&lt;T&gt; capability, @Nullable EnumFacing facing)
    {
        if(capability == CapabilityAnimation.ANIMATION_CAPABILITY)
        {
            return CapabilityAnimation.ANIMATION_CAPABILITY.cast(asm);
        }
        return null;
    }
}</code></pre>

<p>There is no way to receive events on an item in the current implementation.</p>
<h2 id="entities">Entities<a class="headerlink" href="#entities" title="Permanent link"> </a></h2>
<p>In order to animate an entity with the animation API, your entity&rsquo;s renderer must take an <code>AnimationModelBase</code> as its model. This model&rsquo;s constructor
takes two parameters, the location of the actual model to animate (as in the path to the JSON or B3D file, not a blockstate reference) and a <code>VertexLighter</code>.
The <code>VertexLighter</code> object can be created with <code>new VertexLighterSmoothAo(Minecraft.getMinecraft().getBlockColors())</code>.
The entity must also provide the <code>ANIMATION_CAPABILITY</code>, which can be created with its <code>.cast</code> method by passing the ASM.</p>
<p>The <code>handleEvents()</code> callback is located inside the <code>AnimationModelBase</code> class, if you want to use the events you must subclass <code>AnimationModelBase</code>. The callback
takes three parameters: the entity being rendered, the current time in partial ticks, and an iterable of the events that have occurred. </p>
<p>An example of creating the renderer is shown below:</p>
<pre class="highlight"><code class="language-java">ResourceLocation location = new ModelResourceLocation(new ResourceLocation(MODID, blockName), "entity");
return new RenderLiving&lt;EntityChest&gt;(manager, new net.minecraftforge.client.model.animation.AnimationModelBase&lt;EntityChest&gt;(location, new VertexLighterSmoothAo(Minecraft.getMinecraft().getBlockColors()))
{
    @Override
    public void handleEvents(EntityChest chest, float time, Iterable&lt;Event&gt; pastEvents)
    {
        chest.handleEvents(time, pastEvents);
    }
}, 0.5f) {

// ... getEntityTexture() ...

};</code></pre>
</article>