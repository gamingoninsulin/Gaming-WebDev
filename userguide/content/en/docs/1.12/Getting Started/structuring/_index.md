---
linkTitle: Structuring
---

<article class="docs-entry">
<h1 id="structuring-your-mod">Structuring Your Mod<a class="headerlink" href="#structuring-your-mod" title="Permanent link"> </a></h1>
<p>We&rsquo;ll look at how to organize your mod into different files and what those files should do.</p>
<h2 id="packaging">Packaging<a class="headerlink" href="#packaging" title="Permanent link"> </a></h2>
<p>Pick a unique package name. If you own a URL associated with your project, you can use it as your top level package. For example if you own &ldquo;example.com&rdquo;, you may use <code>com.example</code> as your top level package.</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p>If you do not own a domain, do not use it for your top level package. It is perfectly acceptable to start your package with anything, such as your name/nickname, or the name of the mod.</p>
</div>
<p>After the top level package (if you have one) you append a unique name for your mod, such as <code>examplemod</code>. In our case it will end up as <code>com.example.examplemod</code>.</p>
<h2 id="the-mcmodinfo-file">The <code>mcmod.info</code> file<a class="headerlink" href="#the-mcmodinfo-file" title="Permanent link"> </a></h2>
<p>This file defines the metadata of your mod. Its information may be viewed by users from the main screen of the game through the Mods button. A single info file can describe several mods. When a mod is annotated by the <code>@Mod</code> annotation, it may define the <code>useMetadata</code> property, which defaults to <code>false</code>. When <code>useMetadata</code> is <code>true</code>, the metadata within <code>mcmod.info</code> overrides whatever has been defined in the annotation.</p>
<p>The <code>mcmod.info</code> file is formatted as JSON, where the root element is a list of objects and each object describes one modid. It should be stored as <code>src/main/resources/mcmod.info</code>. A basic <code>mcmod.info</code>, describing one mod, may look like this:</p>
<pre class="highlight"><code>[{
  "modid": "examplemod",
  "name": "Example Mod",
  "description": "Lets you craft dirt into diamonds. This is a traditional mod that has existed for eons. It is ancient. The holy Notch created it. Jeb rainbowfied it. Dinnerbone made it upside down. Etc.",
  "version": "1.0.0.0",
  "mcversion": "1.10.2",
  "logoFile": "assets/examplemod/textures/logo.png",
  "url": "minecraftforge.net/",
  "updateJSON": "minecraftforge.net/versions.json",
  "authorList": ["Author"],
  "credits": "I'd like to thank my mother and father."
}]
</code></pre>

<p>The default Gradle configuration replaces <code>${version}</code> with the project version, and <code>${mcversion}</code> with the Minecraft version, but <em>only</em> within <code>mcmod.info</code>, so you should use those instead of directly writing them out. Here is a table of attributes that may be given to a mod, where <code>required</code> means there is no default and the absence of the property causes an error. In addition to the required properties, you should also define <code>description</code>, <code>version</code>, <code>mcversion</code>, <code>url</code>, and <code>authorList</code>.</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="center">Type</th>
<th align="center">Default</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">modid</td>
<td align="center">string</td>
<td align="center">required</td>
<td align="left">The modid this description is linked to. If the mod is not loaded, the description is ignored.</td>
</tr>
<tr>
<td align="right">name</td>
<td align="center">string</td>
<td align="center">required</td>
<td align="left">The user-friendly name of this mod.</td>
</tr>
<tr>
<td align="right">description</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">A description of this mod in 1-2 paragraphs.</td>
</tr>
<tr>
<td align="right">version</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">The version of the mod.</td>
</tr>
<tr>
<td align="right">mcversion</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">The Minecraft version.</td>
</tr>
<tr>
<td align="right">url</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">A link to the mod&rsquo;s homepage.</td>
</tr>
<tr>
<td align="right">updateUrl</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">Defined but unused. Superseded by updateJSON.</td>
</tr>
<tr>
<td align="right">updateJSON</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">The URL to a <a href="../autoupdate/index.htm#forge-update-checker">version JSON</a>.</td>
</tr>
<tr>
<td align="right">authorList</td>
<td align="center">[string]</td>
<td align="center"><code>[]</code></td>
<td align="left">A list of authors to this mod.</td>
</tr>
<tr>
<td align="right">credits</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">A string that contains any acknowledgements you want to mention.</td>
</tr>
<tr>
<td align="right">logoFile</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">The path to the mod&rsquo;s logo. It is resolved on top of the classpath, so you should put it in a location where the name will not conflict, maybe under your own assets folder.</td>
</tr>
<tr>
<td align="right">screenshots</td>
<td align="center">[string]</td>
<td align="center"><code>[]</code></td>
<td align="left">A list of images to be shown on the info page. Currently unimplemented.</td>
</tr>
<tr>
<td align="right">parent</td>
<td align="center">string</td>
<td align="center"><code>""</code></td>
<td align="left">The modid of a parent mod, if applicable. Using this allows modules of another mod to be listed under it in the info page, like BuildCraft.</td>
</tr>
<tr>
<td align="right">useDependencyInformation</td>
<td align="center">boolean</td>
<td align="center"><code>false</code></td>
<td align="left">If true and <code>Mod.useMetadata</code>, the below 3 lists of dependencies will be used. If not, they do nothing.</td>
</tr>
<tr>
<td align="right">requiredMods</td>
<td align="center">[string]</td>
<td align="center"><code>[]</code></td>
<td align="left">A list of modids. If one is missing, the game will crash. This <strong>does not affect the <em>ordering</em> of mod loading!</strong> To specify ordering as well as requirement, have a coupled entry in <code>dependencies</code>.</td>
</tr>
<tr>
<td align="right">dependencies</td>
<td align="center">[string]</td>
<td align="center"><code>[]</code></td>
<td align="left">A list of modids. All of the listed mods will load <em>before</em> this one. If one is not present, nothing happens.</td>
</tr>
<tr>
<td align="right">dependants</td>
<td align="center">[string]</td>
<td align="center"><code>[]</code></td>
<td align="left">A list of modids. All of the listed mods will load <em>after</em> this one. If one is not present, nothing happens.</td>
</tr>
</tbody>
</table>
<p>A good example <code>mcmod.info</code> that uses many of these properties is <a href="../../../../anonymous/05ad9a1e0220bbdc25caed89ef0a22d2.html">BuildCraft</a>.</p>
<h2 id="the-mod-file">The Mod File<a class="headerlink" href="#the-mod-file" title="Permanent link"> </a></h2>
<p>Generally, we&rsquo;ll start with a file named after your mod, and put into your package. This is the <em>entry point</em> to your mod
and will contain some special indicators marking it as such.</p>
<h2 id="what-is-mod">What is <code>@Mod</code>?<a class="headerlink" href="#what-is-mod" title="Permanent link"> </a></h2>
<p>This is an annotation indicating to the Forge Mod Loader that the class is a Mod entry point. It contains various metadata about the mod. It also designates the class that will receive <code>@EventHandler</code> events.</p>
<p>Here is a table of the properties of <code>@Mod</code>:</p>
<table>
<thead>
<tr>
<th align="right">Property</th>
<th align="center">Type</th>
<th align="center">Default</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="right">modid</td>
<td align="center">String</td>
<td align="center">required</td>
<td align="left">A unique identifier for the mod. It must be lowercased, and will be truncated to 64 characters in length.</td>
</tr>
<tr>
<td align="right">name</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">A user-friendly name for the mod.</td>
</tr>
<tr>
<td align="right">version</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">The version of the mod. It should be just numbers seperated by dots, ideally conforming to <a href="../../../../index-2.htm">Semantic Versioning</a>. Even if <code>useMetadata</code> is set to <code>true</code>, it&rsquo;s a good idea to put the version here anyways.</td>
</tr>
<tr>
<td align="right">dependencies</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">Dependencies for the mod. The specification is described in the Forge <code>@Mod</code> javadoc:<br><blockquote><p>A dependency string can start with the following four prefixes: <code>"before"</code>, <code>"after"</code>, <code>"required-before"</code>, <code>"required-after"</code>; then <code>":"</code> and the <code>modid</code>.</p><p>Optionally, a version range can be specified for the mod by adding <code>"@"</code> and then the version range.<a href="#version-ranges">*</a></p><p>If a &ldquo;required&rdquo; mod is missing, or a mod exists with a version outside the specified range, the game will not start and an error screen will tell the player which versions are required.</p></blockquote></td>
</tr>
<tr>
<td align="right">useMetadata</td>
<td align="center">boolean</td>
<td align="center">false</td>
<td align="left">If set to true, properties in <code>@Mod</code> will be overridden by <code>mcmod.info</code>.</td>
</tr>
<tr>
<td align="right">clientSideOnly<br>serverSideOnly</td>
<td align="center">boolean<br>boolean</td>
<td align="center">false<br>false</td>
<td align="left">If either is set to <code>true</code>, the jar will be skipped on the other side, and the mod will not load. If both are true, the game crashes.</td>
</tr>
<tr>
<td align="right">acceptedMinecraftVersions</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">The version range of Minecraft the mod will run on.<a href="#version-ranges">*</a> An empty string will match all versions.</td>
</tr>
<tr>
<td align="right">acceptableRemoteVersions</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">Specifies a remote version range that this mod will accept as valid.<a href="#version-ranges">*</a> <code>""</code> Matches the current version, and <code>"*"</code> matches all versions. Note that <code>"*"</code> matches even when the mod is not present on the remote side at all.</td>
</tr>
<tr>
<td align="right">acceptableSaveVersions</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">A version range specifying compatible save version information.<a href="#version-ranges">*</a> If you follow an unusual version convention, use <code>SaveInspectionHandler</code> instead.</td>
</tr>
<tr>
<td align="right">certificateFingerprint</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">See the tutorial on <a href="../../concepts/jarsigning/index.htm">jar signing</a>.</td>
</tr>
<tr>
<td align="right">modLanguage</td>
<td align="center">String</td>
<td align="center">&ldquo;java&rdquo;</td>
<td align="left">The programming language the mod is written in. Can be either <code>"java"</code> or <code>"scala"</code>.</td>
</tr>
<tr>
<td align="right">modLanguageAdapter</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">Path to a language adapter for the mod. The class must have a default constructor and must implement <code>ILanguageAdapter</code>. If it doesn&rsquo;t, Forge will crash. If set, overrides <code>modLanguage</code>.</td>
</tr>
<tr>
<td align="right">canBeDeactivated</td>
<td align="center">boolean</td>
<td align="center">false</td>
<td align="left">This is not implemented, but if the mod could be deactivated (e.g. a minimap mod), this would be set to <code>true</code> and the mod would <a href="../../events/intro/index.htm#creating-an-event-handler">receive</a> <code>FMLDeactivationEvent</code> to perform cleanup tasks.</td>
</tr>
<tr>
<td align="right">guiFactory</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">Path to the mod&rsquo;s GUI factory, if one exists. GUI factories are used to make custom config screens, and must implement <code>IModGuiFactory</code>. For an example, look at <code>FMLConfigGuiFactory</code>.</td>
</tr>
<tr>
<td align="right">updateJSON</td>
<td align="center">String</td>
<td align="center">&rdquo;&ldquo;</td>
<td align="left">URL to an update JSON file. See <a href="../autoupdate/index.htm">Forge Update Checker</a></td>
</tr>
</tbody>
</table>
<p><a name="version-ranges" style="color: inherit; text-decoration: inherit">* All version ranges use the <a href="../../../../enforcer/enforcer-rules/versionRanges.html">Maven Version Range Specification</a>.</p>
<p>You can find an example mod in the <a href="../../../../net/minecraftforge/forge/index.htm">Forge src download</a>.</p>
<h2 id="keeping-your-code-clean-using-sub-packages">Keeping Your Code Clean Using Sub-packages<a class="headerlink" href="#keeping-your-code-clean-using-sub-packages" title="Permanent link"> </a></h2>
<p>Rather than clutter up a single class and package with everything, it is recommended you break your mod into subpackages.</p>
<p>A common subpackage strategy has packages for <code>common</code> and <code>client</code> code, which is code that can be run on server/client and client, respectively. Inside the <code>common</code> package would go things like Items, Blocks, and Tile Entities (which can each in turn be another subpackage). Things like GUIs and Renderers would go inside the <code>client</code> package.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This package style is only a suggestion, though it is a commonly used style. Feel free to use your own packaging system.</p>
</div>
<p>By keeping your code in clean subpackages, you can grow your mod much more organically.</p>
<h2 id="class-naming-schemes">Class Naming Schemes<a class="headerlink" href="#class-naming-schemes" title="Permanent link"> </a></h2>
<p>A common class naming scheme allows easier deciphering of what a class is, and also makes it easier for someone developing with your mod to find things.</p>
<p>For Example:</p>
<ul>
<li>An <code>Item</code> called <code>PowerRing</code> would be in an <code>item</code> package, with a class name of <code>ItemPowerRing</code>.</li>
<li>A <code>Block</code> called <code>NotDirt</code> would be in a <code>block</code> package, with a class name of <code>BlockNotDirt</code>.</li>
<li>Finally, a <code>TileEntity</code> for a block called <code>SuperChewer</code> would be a <code>tile</code> or <code>tileentity</code> package, with a class name of <code>TileSuperChewer</code>.</li>
</ul>
<p>Prepending your class names with what <em>kind</em> of object they are makes it easier to figure out what a class is, or guess the class for an object.</p>
</article>