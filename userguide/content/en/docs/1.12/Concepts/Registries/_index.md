---
LinkTitle: Registries
---

<article class="docs-entry">
<h1 id="registries">Registries<a class="headerlink" href="#registries" title="Permanent link"> </a></h1>
<p>Registration is the process of taking the objects of a mod (items, blocks, sounds, etc.) and making them known to the game. Registering things is important, as without registration the game will simply not know about these objects in a mod and will exhibit great amounts of unexplainable behavior (and probably crash). Some examples of things that need to be registered are <code>Block</code>s, <code>Item</code>s, <code>Biome</code>s.</p>
<p>Most things that require registration in the game are handled by the Forge registries. A registry is a simple object similar to a map that assigns values to keys. Additionally, they automatically assign integer IDs to values. Forge uses registries with <a href="../resources/index.htm#resourcelocation"><code>ResourceLocation</code></a> keys to register objects. This allows the <code>ResourceLocation</code> to act like a &ldquo;registry name&rdquo; for the object. The registry name for an object may be accessed with <code>get</code>/<code>setRegistryName</code>. The setter can only ever be called once, and calling it twice results in an exception. Every type of registrable object has its own registry, and names in two different registries will not collide. (E.g. there&rsquo;s a registry for <code>Block</code>s, and a registry for <code>Item</code>s, and a <code>Block</code> and an <code>Item</code> may be registered with the same name <code>mod:example</code> without colliding. However, if two blocks were registered with that name, an exception would be thrown.)</p>
<h2 id="registering-things">Registering Things<a class="headerlink" href="#registering-things" title="Permanent link"> </a></h2>
<p>The recommended way to register things is through the <code>RegistryEvent</code>s. These <a href="../../events/intro/index.htm">events</a> are fired right after preinitialization, In <code>RegistryEvent.NewRegistry</code>, registries should be created. Later, <code>RegistryEvent.Register</code> is fired once for each registered registry. Because <code>Register</code> is a generic event, the event handler should set the type parameter to the type of the object being registered. The event will contain the registry to register things to (<code>getRegistry</code>), and things may be registered with <code>register</code> (or <code>registerAll</code>) on the registry. Here&rsquo;s an example of an event handler that registers blocks:</p>
<pre class="highlight"><code class="language-java">@SubscribeEvent
public void registerBlocks(RegistryEvent.Register&lt;Block&gt; event) {
    event.getRegistry().registerAll(block1, block2, ...);
}</code></pre>

<p>The order in which <code>RegistryEvent.Register</code> events fire is alphabetically, with the exception that <code>Block</code> will <em>always</em> fire first, and <code>Item</code> will <em>always</em> fire second, right after <code>Block</code>. After the <code>Register&lt;Block&gt;</code> event has fired, all <a href="#injecting-registry-values-into-fields"><code>ObjectHolder</code></a> annotations are refreshed, and after <code>Register&lt;Item&gt;</code> has fired they are refreshed again. They are refreshed for a third time after <em>all</em> of the other <code>Register</code> events have fired.</p>
<p><code>RegistryEvent</code>s are currently supported for the following types: <code>Block</code>, <code>Item</code>, <code>Potion</code>, <code>Biome</code>, <code>SoundEvent</code>, <code>PotionType</code>, <code>Enchantment</code>, <code>IRecipe</code>, <code>VillagerProfession</code>, <code>EntityEntry</code></p>
<p>There is another, older way of registering objects into registries, using <code>GameRegistry.register</code>. Anytime something suggests using this method, it should be replaced with an event handler for the appropriate registry event. This method simply finds the registry corresponding to an <code>IForgeRegistryEntry</code> with <code>IForgeRegistryEntry::getRegistryType</code>, and then registers the object to the registry. There is also a convenience overload that takes an <code>IForgeRegistryEntry</code> and a <code>ResourceLocation</code>, which is equivalent to calling <code>IForgeRegistryEntry::setRegistryName</code>, followed by a <code>GameRegistry.register</code> call.</p>
<div class="admonition information">
<p class="admonition-title">Information</p>
<p>Registering an <code>Entity</code> might be a little bit confusing at first as it doesn&rsquo;t use the <code>Entity</code> class, but an <code>EntityEntry</code>. These are created by making use of <code>EntityEntryBuilder</code>.
<code>EntityEntryBuilder#id()</code> is equivalent to the <code>setRegistryName()</code> method from <code>IForgeRegistryEntry</code>, with the difference that it also takes a mod internal int ID. A simple counter during registration is enough as this ID is only used for networking.</p>
</div>
<h2 id="creating-registries">Creating Registries<a class="headerlink" href="#creating-registries" title="Permanent link"> </a></h2>
<p>There&rsquo;s a global registry where all the other registries are stored. By taking a <code>Class</code> that a registry is supposed to store or its <code>ResourceLocation</code> name, one can retrieve a registry from this registry. For example, one can use <code>GameRegistry.findRegistry(Block.class)</code> to get the registry for blocks. Any mod can create their own registries, and any mod can register things to registries from any other mod. Registries are created by using <code>RegistryBuilder</code> inside a <code>RegistryEvent.NewRegistry</code> event handler. This class takes certain parameters for the registry it will generate, such as the name, the <code>Class</code> of it&rsquo;s values, and various callbacks for when the registry is changed. Upon calling <code>RegistryBuilder::create</code>, the registry is built, registered to the metaregistry, and returned to the caller.</p>
<p>In order for a class to have a registry, it needs to implement <code>IForgeRegistryEntry</code>. This interface defines <code>getRegistryName(ResourceLocation)</code>, <code>setRegistryName(ResourceLocation)</code>, and <code>getRegistryType()</code>. <code>getRegistryType</code> is the base <code>Class</code> of the registry the object is to be registered to. It is recommended to extend the default <code>IForgeRegistryEntry.Impl</code> class instead of implementing <code>IForgeRegistryEntry</code> directly. This class also provides two convenience implementations of <code>setRegistryName</code>: one where the parameter is a single string, and one where there are two string parameters. The overload that takes a single string checks whether the input contains a <code>:</code> (i.e. it checks whether the passed in stringified <code>ResourceLocation</code> has a namespace), and if it doesn&rsquo;t, it uses the current modid as the resource namespace. The two argument overload simply constructs the registry name using the <code>modID</code> as the namespace and <code>name</code> as the path.</p>
<h2 id="injecting-registry-values-into-fields">Injecting Registry Values Into Fields<a class="headerlink" href="#injecting-registry-values-into-fields" title="Permanent link"> </a></h2>
<p>It is possible to have Forge inject values from registries into <code>public static final</code> fields of classes. This is done by annotating classes and fields with <code>@ObjectHolder</code>. If a class has this annotation, all the <code>public static final</code> fields within are taken to be object holders too, and the value of the annotation is the namespace of the holder (i.e. every field uses it as the default namespace for the registry name of the object to inject). If a field has this annotation, and the value does not contain a namespace, the namespace is chosen from the surrounding class&rsquo;s <code>@ObjectHolder</code> annotation. If the class is not annotated in this situation, the field is ignored with a warning. If it does contain a namespace, then the object to inject into the field is the object with that name. If the class has the annotation and one of the <code>public static final</code> fields does not, then the resource path of the object&rsquo;s name is taken to be the field&rsquo;s name. The type of the registry is taken from the type of the field.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>If an object is not found, either because the object itself hasn&rsquo;t been registered or because the registry does not exist, a debug message is logged and the field is left unchanged.</p>
</div>
<p>As these rules are rather complicated, here are some examples:</p>
<pre class="highlight">

<code class="language-java">@ObjectHolder("minecraft") // Resource namespace "minecraft"
class AnnotatedHolder {
    public static final Block diamond_block = null; // public static final is required.
                                                    // Type Block means that the Block registry will be queried.
                                                    // diamond_block is the field name, and as the field is not annotated it is taken to be the resource path.
                                                    // As there is no explicit namespace, "minecraft" is inherited from the class.
                                                    // Object to be injected: "minecraft:diamond_block" from the Block registry.

    @ObjectHolder("ender_eye")
    public static final Item eye_of_ender = null;   // Type Item means that the Item registry will be queried.
                                                    // As the annotation has the value "ender_eye", that overrides the field's name.
                                                    // As the namespace is not explicit, "minecraft" is inherited from the class.
                                                    // Object to be injected: "minecraft:ender_eye" from the Item registry.

    @ObjectHolder("neomagicae:coffeinum")
    public static final ManaType coffeinum = null;  // Type ManaType means that the ManaType registry will be queried. This is obviously a registry made by a mod.
                                                    // As the annotation has the value "neomagicae:coffeinum", that overrides the field's name.
                                                    // The namespace is explicit, and is "neomagicae", overriding the class's "minecraft" default.
                                                    // Object to be injected: "neomagicae:coffeinum" from the ManaType registry.

    public static final Item ENDER_PEARL = null;    // Note that the actual name is "minecraft:ender_pearl", not "minecraft:ENDER_PEARL".
                                                    // However, since constructing a ResourceLocation lowercases the value, this will work.
}
</code>

<code class="language-java">
class UnannotatedHolder { // Note lack of annotation on this class.
    @ObjectHolder("minecraft:flame")
    public static final Enchantment flame = null;   // No annotation on the class means that there is no preset namespace to inherit.
                                                    // Field annotation supplies all the information for the object.
                                                    // Object to be injected: "minecraft:flame" from the Enchantment registry.

    public static final Biome ice_flat = null;      // No annotation on the class or the field.
                                                    // Therefore this just gets ignored.
    @ObjectHolder("levitation")
    public static final Potion levitation = null;   // No resource namespace in annotation, and no default specified by class annotation.
                                                    // Therefore, THIS WILL FAIL. The field annotation needs a namespace, or the class needs an annotation.
}
</code>


</article>