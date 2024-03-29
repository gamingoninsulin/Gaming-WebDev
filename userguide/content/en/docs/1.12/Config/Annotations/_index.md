---
Linktitle: Annotations
---

article class="docs-entry">
<h1 id="config"><code>@Config</code><a class="headerlink" href="#config" title="Permanent link"> </a></h1>
<p>The <code>@Config</code> annotation is an alternative to <code>Configuration</code>. </p>
<h2 id="table-of-contents">Table of Contents<a class="headerlink" href="#table-of-contents" title="Permanent link"> </a></h2>
<ul>
<li><a href="#basics">Basics</a></li>
<li><a href="#config-use">@Config Use</a></li>
<li><a href="#comment-use">@Comment Use</a></li>
<li><a href="#name-use">@Name Use</a></li>
<li><a href="#rangeint-use">@RangeInt Use</a></li>
<li><a href="#rangedouble-use">@RangeDouble Use</a></li>
<li><a href="#langkey-use">@LangKey Use</a></li>
<li><a href="#requiresmcrestart-use">@RequiresMcRestart Use</a></li>
<li><a href="#requiresworldrestart-use">@RequiresWorldRestart Use</a></li>
<li><a href="#sub-categories">Sub Categories</a></li>
<li><a href="#ignore-use">@Ignore</a></li>
</ul>
<h2 id="basics">Basics<a class="headerlink" href="#basics" title="Permanent link"> </a></h2>
<p>A class annotated with <code>@Config</code> will have any fields turned into config options. Said fields can be annotated to add information, using the plethora of annotations provided in the <code>@Config</code> class.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Enum config values generate a comment that is as many lines long as the enum has values.</p>
</div>
<p>A good example of this system is <a href="../../../../MinecraftForge/MinecraftForge/blob/603903db507a483fefd90445fd2b3bdafeb4b5e0/src/test/java/net/minecraftforge/debug/ConfigTest.java.html"><code>Forge's Test</code></a></p>
<h2 id="config-use">@Config Use<a class="headerlink" href="#config-use" title="Permanent link"> </a></h2>
<p>This annotation is used to denote a class is the container for some configuration options. </p>
<p>There are 4 properties:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
<th align="left">Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">modid</td>
<td align="left"><code>String</code></td>
<td align="center">N/A</td>
<td align="left">The mod id that this configuration is associated with.</td>
</tr>
<tr>
<td align="right">name</td>
<td align="left"><code>String</code></td>
<td align="center"><code>""</code></td>
<td align="left">A user friendly name for the config file, the default will be modid.</td>
</tr>
<tr>
<td align="right">type</td>
<td align="left"><code>Type</code></td>
<td align="center"><code>Type.INSTANCE</code></td>
<td align="left">The type this is, right now the only value is <code>Type.INSTANCE</code>. This is intended to be expanded upon later for more Forge controlled configs.</td>
</tr>
<tr>
<td align="right">category</td>
<td align="left"><code>String</code></td>
<td align="center"><code>"general"</code></td>
<td align="left">Root element category. If this is an empty string then the root category is disabled.</td>
</tr>
</tbody>
</table>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>If you disable the root category, this will cause issues unless you create <a href="#sub-categories">Sub Categories</a>.</p>
</div>
<h2 id="comment-use">@Comment Use<a class="headerlink" href="#comment-use" title="Permanent link"> </a></h2>
<p>Adding a <code>@Comment</code> annotation to a field will add a comment to the config in its file. </p>
<p>It has 1 property:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
<th align="left">Comment</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">value</td>
<td align="left"><code>String[]</code>/<code>String</code></td>
<td align="center">N/A</td>
<td align="left">The passed value will be converted to a <code>String[]</code> and for each element of the <code>String[]</code> a new line is added to the comment.</td>
</tr>
</tbody>
</table>
<h3 id="example">Example<a class="headerlink" href="#example" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@Comment({
  "You can add comments using this",
  "and if you supply an array it will be multi-line"
})
public static boolean doTheThing = true;</code></pre>
This would produce the following config:
<pre class="highlight"><code># You can add comments using this
# and if you supply an array it will be multi-line
B:doTheThing=true   </code></pre>
<h2 id="name-use">@Name Use<a class="headerlink" href="#name-use" title="Permanent link"> </a></h2>
<p>You should use the <code>@Name</code> annotation to give a config a user friendly name in the config file.</p>
<p>This has 1 property:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">value</td>
<td align="left"><code>String</code></td>
<td align="center">N/A</td>
</tr>
</tbody>
</table>
<h3 id="example_1">Example<a class="headerlink" href="#example_1" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@Name("FE/T for the thing")
public static int thingFE = 50; </code></pre>
This will produce the following config:
<pre class="highlight"><code>I:"FE/T for the thing"=50</code></pre>
<h2 id="rangeint-use">@RangeInt Use<a class="headerlink" href="#rangeint-use" title="Permanent link"> </a></h2>
<p>You should use <code>@RangeInt</code> to limit an <code>int</code> or <code>Integer</code> config value. </p>
<p>It has 2 properties:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">min</td>
<td align="left"><code>int</code></td>
<td align="center"><code>Integer.MIN_VALUE</code></td>
</tr>
<tr>
<td align="right">max</td>
<td align="left"><code>int</code></td>
<td align="center"><code>Integer.MAX_VALUE</code></td>
</tr>
</tbody>
</table>
<h3 id="example_2">Example<a class="headerlink" href="#example_2" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@RangeInt(min = 0) 
public static int thingFECapped = 50;</code></pre>
This will produce the following config:
<pre class="highlight"><code># Min: 0
# Max: 2147483647
I:thingFECapped=50</code></pre>

<h2 id="rangedouble-use">@RangeDouble Use<a class="headerlink" href="#rangedouble-use" title="Permanent link"> </a></h2>
<p>You should use <code>@RangeDouble</code> to limit a <code>double</code> or <code>Double</code> config value. </p>
<p>It has 2 properties:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">min</td>
<td align="left"><code>double</code></td>
<td align="center"><code>Double.MIN_VALUE</code></td>
</tr>
<tr>
<td align="right">max</td>
<td align="left"><code>double</code></td>
<td align="center"><code>Double.MAX_VALUE</code></td>
</tr>
</tbody>
</table>
<h3 id="example_3">Example<a class="headerlink" href="#example_3" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@RangeDouble(min = 0, max = Math.PI) 
public static double chanceToDrop = 2;</code></pre>
This will produce the following config:
<pre class="highlight"><code># Min: 0.0
# Max: 3.141592653589793
D:chanceToDrop=2.0</code></pre>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>There is not currently (in 1.12.2) a <code>@RangedFloat</code> or <code>@RangedLong</code> or any other variant of the Ranged values. </p>
</div>
<h2 id="langkey-use">@LangKey Use<a class="headerlink" href="#langkey-use" title="Permanent link"> </a></h2>
<p>If you want to add translations for your configs in the mod options menu, add to the config&rsquo;s field <code>@LangKey</code>.</p>
<p>This has 1 property:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="left">Type</th>
<th align="center">Default Value</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">value</td>
<td align="left"><code>String</code></td>
<td align="center">N/A</td>
</tr>
</tbody>
</table>
<h2 id="requiresmcrestart-use">@RequiresMcRestart Use<a class="headerlink" href="#requiresmcrestart-use" title="Permanent link"> </a></h2>
<p>You should include this annotation on a config that if changed, will require the game to be restarted for the change to take affect.</p>
<h3 id="example_4">Example<a class="headerlink" href="#example_4" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@RequiresMcRestart
public static boolean overlayEnabled = false;</code></pre>
This will make the game require a restart if the value is changed in the configs menu.
<h2 id="requiresworldrestart-use">@RequiresWorldRestart Use<a class="headerlink" href="#requiresworldrestart-use" title="Permanent link"> </a></h2>
<p>This will force the world to be restarted if the config is changed in the mod options menu.</p>
<h3 id="example_5">Example<a class="headerlink" href="#example_5" title="Permanent link"> </a></h3>
<p><pre class="highlight"><code class="language-java">@RequiresWorldRestart
public static boolean someOtherworldlyThing = false;</code></pre>
This will force the world to be restarted if the config is changed in the mod options menu.
<h2 id="sub-categories">Sub Categories<a class="headerlink" href="#sub-categories" title="Permanent link"> </a></h2>
<p>A Sub Category is a way to group certain (usually related) config options together, and should be used to help make navigating your config file easier. To create a Sub Category, you must make an object and add it as a static field in the parent category&rsquo;s class. The object&rsquo;s member fields will become configs in that Sub Category.</p>
<p>An example of how to setup a Sub Category:
<pre class="highlight"><code class="language-java">@Config(modid = "modid")
public class Configs {
  public static SubCategory subcat = new SubCategory();

  private static class SubCategory {
    public boolean someBool; 
    public int relatedInt;
  }
}</code></pre>
In the config file, this will produce the following:
<pre class="highlight"><code>subcat {
  B:someBool=false
  I:relatedInt=0
}</code></pre>
<h2 id="ignore-use">@Ignore Use<a class="headerlink" href="#ignore-use" title="Permanent link"> </a></h2>
<p>Adding the <code>@Ignore</code> annotation to a field in the config class will cause the <code>ConfigManager</code> to skip over it when processing your config file. </p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This will only work on forge version 1.12.2-14.23.1.2602 and later, as the feature was added in <a href="../../../../MinecraftForge/MinecraftForge/commit/ca7a5eadc05c427a21fb7ae745e5fd9a5d906267.html">this update</a></p>
</div>
</article>