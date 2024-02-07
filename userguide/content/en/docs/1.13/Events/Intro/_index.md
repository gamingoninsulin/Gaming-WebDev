---
linkTitle: Intro
---

<article class="docs-entry">
<h1 id="events">Events<a class="headerlink" href="#events" title="Permanent link"> </a></h1>
<p>Forge uses an event bus that allows mods to intercept events from various vanilla and mod behaviors.</p>
<p>Example: An event can be used to perform an action when a Vanilla stick is right clicked.</p>
<p>The main event bus used for most events is located at <code>MinecraftForge.EVENT_BUS</code>. </p>
<p>An event handler is a class that contains one or more <code>public void</code> member methods that are marked with the <code>@SubscribeEvent</code> annotation.</p>
<h2 id="creating-an-event-handler">Creating an Event Handler<a class="headerlink" href="#creating-an-event-handler" title="Permanent link"> </a></h2>
<p><pre class="highlight"><code class="language-java">public class MyForgeEventHandler {
    @SubscribeEvent
    public void pickupItem(EntityItemPickupEvent event) {
        System.out.println("Item picked up!");
    }
}</code></pre>
This event handler listens for the <code>EntityItemPickupEvent</code>, which is, as the name states, posted to the event bus whenever an <code>Entity</code> picks up an item.
<p>To register this event handler, use <code>MinecraftForge.EVENT_BUS.register()</code> and pass it an instance of your event handler class.</p>
<h3 id="static-event-handlers">Static Event Handlers<a class="headerlink" href="#static-event-handlers" title="Permanent link"> </a></h3>
<p>An event handler may also be static. The handling method is still annotated with <code>@SubscribeEvent</code> and the only difference from an instance handler is that it is also marked <code>static</code>. In order to register a static event handler, an instance of the class won&rsquo;t do, the <code>Class</code> itself has to be passed in. An example:</p>
<pre class="highlight"><code class="language-java">public class MyStaticForgeEventHandler {
    @SubscribeEvent
    public static void arrowNocked(ArrowNockEvent event) {
        System.out.println("Arrow nocked!");
    }
}</code></pre>

<p>which must be registered like this: <code>MinecraftForge.EVENT_BUS.register(MyStaticForgeEventHandler.class)</code>.</p>
<h3 id="automatically-registering-static-event-handlers">Automatically Registering Static Event Handlers<a class="headerlink" href="#automatically-registering-static-event-handlers" title="Permanent link"> </a></h3>
<p>A class may be annotated with the <code>@Mod.EventBusSubscriber</code> annotation. Such a class is automatically registered to <code>MinecraftForge.EVENT_BUS</code> when the <code>@Mod</code> class itself is constructed. This is essentially equivalent to adding <code>MinecraftForge.EVENT_BUS.register(AnnotatedClass.class);</code> at the end of the <code>@Mod</code> class&rsquo;s constructor.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This does not register an instance of the class; it registers the class itself (i.e. the event handling methods must be static).</p>
</div>
<h2 id="canceling">Canceling<a class="headerlink" href="#canceling" title="Permanent link"> </a></h2>
<p>If an event can be canceled, it will be marked with the <code>@Cancelable</code> annotation, and the method <code>Event#isCancelable()</code> will return <code>true</code>. The cancel state of a cancelable event may be modified by calling <code>Event#setCanceled(boolean canceled)</code>, wherin passing the boolean value <code>true</code> is interpreted as canceling the event, and passing the boolean value <code>false</code> is interpreted as &ldquo;un-canceling&rdquo; the event. However, if the event cannot be canceled (as defined by <code>Event#isCancelable()</code>), an <code>UnsupportedOperationException</code> will be thrown regardless of the passed boolean value, since the cancel state of a non-cancelable event event is considered immutable.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Not all events can be canceled! Attempting to cancel an event that is not cancelable will result in an unchecked <code>UnsupportedOperationException</code> being thrown, which is expected to result in the game crashing! Always check that an event can be canceled using <code>Event#isCancelable()</code> before attempting to cancel it!</p>
</div>
<h2 id="results">Results<a class="headerlink" href="#results" title="Permanent link"> </a></h2>
<p>Some events have an <code>Event.Result</code>, a result can be one of three things, <code>DENY</code> which stops the event, <code>DEFAULT</code> which uses the Vanilla behavior, and <code>ALLOW</code> which forces the action to take place, regardless if it would have originally. The result of an event can be set by calling <code>setResult</code> with an <code>Event.Result</code> on the event. Not all events have results, an event with a result will be annotated with <code>@HasResult</code>.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>Different events may use results in different ways, refer to the event&rsquo;s JavaDoc before using the result.</p>
</div>
<h2 id="priority">Priority<a class="headerlink" href="#priority" title="Permanent link"> </a></h2>
<p>Event handler methods (marked with <code>@SubscribeEvent</code>) have a priority. You can set the priority of an event handler method by setting the <code>priority</code> value of the annotation. The priority can be any value of the <code>EventPriority</code> enum (<code>HIGHEST</code>, <code>HIGH</code>, <code>NORMAL</code>, <code>LOW</code>, and <code>LOWEST</code>). Event handlers with priority <code>HIGHEST</code> are executed first and from there in descending order until <code>LOWEST</code> events which are executed last.</p>
<h2 id="sub-events">Sub Events<a class="headerlink" href="#sub-events" title="Permanent link"> </a></h2>
<p>Many events have different variations of themselves, these can be different but all based around one common factor (e.g. <code>PlayerEvent</code>) or can be an event that has multiple phases (e.g. <code>PotionBrewEvent</code>). Take note that if you listen to the parent event class, you will receive calls to your method for <em>all</em> subclasses.</p>
</article>