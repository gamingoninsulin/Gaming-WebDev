---
linkTitle: exented BlockStates
---

<article class="docs-entry">
<h1 id="extended-blockstates">Extended Blockstates<a class="headerlink" href="#extended-blockstates" title="Permanent link"> </a></h1>
<p>Extended blockstates provide a way for blocks to pass arbitrary data to their models. Ordinary blockstates and properties occupy only a fixed set of possible states, while extended states can form infinite sets. This accomplished through the use of unlisted properties (<code>IUnlistedProperty&lt;V&gt;</code>), extended block states (<code>IExtendedBlockState</code>), and extended block state containers (<code>ExtendedBlockState</code>).</p>
<p>Ordinary block state containers (<code>BlockStateContainer</code>) take a set of listed properties (<code>IProperty&lt;V&gt;</code>) that define all possible values for themselves and use those to create all possible combinatons of values. These combinations are then turned into <code>IBlockState</code>s and stored in the finite set of all possible states. The properties are called &ldquo;listed&rdquo; as they appear on the F3 debug screen, all the way to the right.</p>
<p>Extended block state containers take a set of listed properties, and also a set of <em>unlisted</em> properties. Unlisted properties&rsquo; values can be anything that matches their type, and are not limited to a finite set. However, values are still required to satisfy the predicate <code>IUnlistedProperty::isValid</code>. The listed properties are again used to create the set of states, this time <code>IExtendedBlockState</code>s, but the unlisted properties remain unset. The unlisted properties are only set when <code>IExtendedBlockState::withProperty</code> is called to do so.</p>
<p>Most of the methods on <code>IExtendedBlockState</code> are self-explanatory, maybe with the exception of <code>getClean</code>. <code>getClean</code> returns the base <code>IBlockState</code> with none of the unlisted properties set.</p>
<h2 id="why-extended-blockstates">Why Extended Blockstates<a class="headerlink" href="#why-extended-blockstates" title="Permanent link"> </a></h2>
<p>Why use extended blockstates at all? Why not simply pass an <code>IBlockAccess</code> and <code>BlockPos</code> into the rendering system directly and have the <a href="../ibakedmodel/index.htm"><code>IBakedModel</code></a> itself deal with it? Indeed, extended blockstates allow this to happen anyway! Why have the system at all when we could just do that? The reason is that it makes the system more flexible. External code can take an <code>IExtendedBlockState</code> and fill in some data by itself, overriding the block&rsquo;s own data. This allows that code to alter the model in ways that would be impossible if it was just a black box that took a world and a position and spat out some geometry.</p>
<p>One such use case is advanced camouflaging blocks. The camouflaging block may have a tile entity that holds the <code>IBlockState</code> representative of the camouflagee, and an unlisted property to hold that state during rendering. This property can then be filled by <code>getExtendedState</code>. Finally, a custom <a href="../ibakedmodel/index.htm"><code>IBakedModel</code></a> can steal the model for that state and use it instead of using the uncamouflaged model.</p>
<h2 id="declaring-unlisted-properties">Declaring Unlisted Properties<a class="headerlink" href="#declaring-unlisted-properties" title="Permanent link"> </a></h2>
<p>Unlisted properties are declared in <code>Block.createBlockState</code>, the same place as regular (&ldquo;listed&rdquo;) properties. Instead of returning a <code>BlockStateContainer</code>, one must return an <code>ExtendedBlockState</code>. Forge provides a builder <code>BlockStateContainer.Builder</code>, which will automatically handle returning an <code>ExtendedBlockState</code> for you.</p>
<p>Example:
<pre class="highlight"><code class="language-Java">@Override
public BlockStateContainer createBlockState() {
    return new BlockStateContainer.Builder(this).add(LISTED_PROP).add(UNLISTED_PROP).build();
}</code></pre>
<p>Note that you do not need to set default values for your unlisted properties. </p>
<h2 id="filling-extended-states">Filling Extended States<a class="headerlink" href="#filling-extended-states" title="Permanent link"> </a></h2>
<p>Before an <code>IBlockState</code> is passed to an <code>IBakedModel</code>, it will always have <code>Block.getExtendedState</code> called on it first. In this method, you will give all your unlisted properties values. Assuming you registered at least one unlisted property in the previous section, the <code>IBlockState</code> parameter can be safely casted to <code>IExtendedBlockState</code>, which has a <code>withProperty</code> method for unlisted properties analogous to its listed property cousin. Here, you can query whatever you want from the World, the Tile Entity, etc. (with appropriate safety checks, of course) and insert it into the extended blockstate.</p>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>It is highly recommended that your unlisted property values be immutable. Baked model implementations will use the extended state and unlisted values on multiple threads, so any value must be used in a threadsafe manner. The easiest way is to simply make that information an immutable snapshot. Anything you might possibly want to know in your custom <code>IBakedModel</code>, you should be passing an immutable snapshot of through <code>Block.getExtendedState</code>.</p>
</div>
<p>Example:
<pre class="highlight"><code class="language-Java">@Override
public IExtendedBlockState getExtendedState(IBlockState state, IBlockAccess world, BlockPos pos) {
    IExtendedBlockState ext = (IExtendedBlockState) state;
    TileEntity te = world.getTileEntity(pos);
    if (te instanceof MyTE) {
        ext = ext.withProperty(UNLISTED_PROP, ((MyTE) te).getSomeImmutableData());
    }
    return ext;
}</code></pre>
<h2 id="using-extended-states">Using Extended States<a class="headerlink" href="#using-extended-states" title="Permanent link"> </a></h2>
<p>In a custom <code>IBakedModel</code>, the <code>IBlockState</code> parameter passed to you will be exactly the object you returned in <code>Block.getExtendedState</code>, and you can pull data out of it and use it to affect your rendering as you wish.</p>
<p>Here is a basic example of using an unlisted property to determine which model to render, assuming <code>UNLISTED_PROP</code> is an <code>IUnlistedProperty&lt;String&gt;</code>:
<pre class="highlight"><code class="language-Java">// in custom IBakedModel
private final Map&lt;String, IBakedModel&gt; submodels = new HashMap&lt;&gt;(); // populated in a custom manner out of the scope of this article

@Override
public List&lt;BakedQuad&gt; getQuads(@Nullable IBlockState state, EnumFacing facing, long rand) {
    IBakedModel fallback = Minecraft.getMinecraft().getBlockRendererDispatcher().getBlockModelShapes().getModelManager().getMissingModel();
    if (state == null)
        return fallback.getQuads(state, facing, rand);

    IExtendedBlockState ex = (IExtendedBlockState) state;
    String id = ex.getValue(UNLISTED_PROP);
    return submodels.getOrDefault(id, fallback).getQuads(state, facing, rand);
}</code></pre>
<p>Since there are no longer a fixed set of <code>IExtendedBlockState</code> generated at startup, comparisons involving an extended state must use <code>getClean()</code>, which is a method on <code>IExtendedBlockState</code> that returns the vanilla state (i.e. the fixed set of <code>IBlockState</code> with only the listed properties).
<pre class="highlight"><code class="language-Java">IExtendedBlockState state = ...;
IBlockState worldState = world.getBlockState(pos);
if(state.getClean() == worldState) {
    ...
}</code></pre>
</article>