---
LinkTitle: Internationalization
---

<article class="docs-entry">
<h1 id="internationalization-and-localization">Internationalization and localization<a class="headerlink" href="#internationalization-and-localization" title="Permanent link"> </a></h1>
<p>Internationalization, i18n for short, is a way of designing code so that it requires no changes to be adapted for various languages. Localization is the process of adapting displayed text to the user&rsquo;s language.</p>
<p>I18n is implemented using <em>translation keys</em>. A translation key is a string that identifies a piece of displayable text in no specific language. For example, <code>tile.dirt.name</code> is the translation key referring to the name of the Dirt block. This way, displayable text may be referenced with no concern for a specific language. The code requires no changes to be adapted in a new language.</p>
<p>Localization will happen in the game&rsquo;s locale. In a Minecraft client the locale is specified by the language settings. On a dedicated server, the only supported locale is en_US. A list of available locales can be found on the <a href="../../../../wiki/Language.html#Available_languages">Minecraft Wiki</a>.</p>
<h2 id="language-files">Language files<a class="headerlink" href="#language-files" title="Permanent link"> </a></h2>
<p>Language files are located by <code>assets/[namespace]/lang/[locale].lang</code> (e.g. the US English translation for <code>examplemod</code> would be <code>assets/examplemod/lang/en_us.lang</code>). Resource pack format 3 (as specified in pack.mcmeta) requires the locale name to be lowercased. The file format is simply lines of key-value pairs separated by <code>=</code>. Lines starting with <code>#</code> are ignored. Lines without a separator are ignored. The file must be encoded in UTF-8.</p>
<pre class="highlight"><code class="language-properties">

# items
item.examplemod.example_item.name=Example Item Name

# blocks
tile.examplemod.example_block.name=Example Block Name

# commands
commands.examplemod.examplecommand.usage=/example &lt;value&gt;</code></pre>

<p>Including the comment <code>#PARSE_ESCAPES</code> anywhere in the file will enable the slightly different and more complex Java properties file format. This format is required to support multiline values.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Enabling <code>#PARSE_ESCAPES</code> changes various aspects of the parsing. Among other things, the <code>:</code> character will be treated as a key-value separator. It must either be excluded from translation keys or escaped using <code>\:</code>.</p>
</div>
<h2 id="usage-with-blocks-and-items">Usage with Blocks and Items<a class="headerlink" href="#usage-with-blocks-and-items" title="Permanent link"> </a></h2>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>As of July 14th 2018, the term &ldquo;unlocalized name&rdquo; has been replaced by &ldquo;translation key&rdquo; in MCP.</p>
</div>
<p>Block, Item and a few other Minecraft classes have built-in translation keys used to display their names. These translation keys are specified by calling <code>setTranslationKey(String)</code> or by overriding <code>getTranslationKey()</code>. Item also has <code>getTranslationKey(ItemStack)</code> which can be overridden to provide different translation keys depending on ItemStack damage or NBT.</p>
<p>By default, <code>getTranslationKey()</code> will return <code>tile.</code> or <code>item.</code> prepended to whatever was set with <code>setTranslationKey(String)</code>, and ItemBlock will inherit the unlocalized name of its Block. Additionally, <code>.name</code> is always appended to the translation key before it is localized. For example <code>item.setTranslationKey("examplemod.example_item")</code> effectively requires the following line in a language file:</p>
<pre class="highlight"><code class="language-properties">item.examplemod.example_item.name=Example Item Name</code></pre>

<p>Unlike registry names, translation keys are not namespaced. It is therefore highly recommended to prefix your modid somewhere to the translation key (e.g. <code>examplemod.example_item</code>) to avoid naming conflicts. Otherwise, in the event of a conflict, the localization of one object will override the other.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The only purpose of a translation key is internationalization. Do not use them for logic. Use registry names instead.</p>
<p>A common pattern is using <code>getUnlocalizedName().substring(5)</code> to assign registry names. This is fragile and uses the translation key for logic, which is considered bad practice. Consider setting the registry name first, then setting the translation key according to the registry name using <code>MODID + "." + getRegistryName().getResourcePath()</code>.</p>
</div>
<h2 id="localization-methods">Localization methods<a class="headerlink" href="#localization-methods" title="Permanent link"> </a></h2>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>A common issue is having the server localize for clients. The server can only localize in its own locale, which does not necessarily match the locale of connected clients.</p>
<p>To respect the language settings of clients, the server should have clients localize text in their own locale using <code>TextComponentTranslation</code> or other methods preserving the language neutral translation keys.</p>
</div>
<h3 id="netminecraftclientresourcesi18n-client-only"><code>net.minecraft.client.resources.I18n</code> (client only)<a class="headerlink" href="#netminecraftclientresourcesi18n-client-only" title="Permanent link"> </a></h3>
<p><strong>This I18n class can only be found on a Minecraft client!</strong> It is intended to be used by code that only runs on the client. Attempts to use this on a server will throw exceptions and crash.</p>
<ul>
<li><code>format(String, Object...)</code> localizes in the client&rsquo;s locale with formatting. The first parameter is a translation key, and the rest are formatting arguments for <code>String.format(String, Object...)</code>.</li>
</ul>
<h3 id="netminecraftutiltexttranslationi18n-deprecated"><code>net.minecraft.util.text.translation.I18n</code> (deprecated)<a class="headerlink" href="#netminecraftutiltexttranslationi18n-deprecated" title="Permanent link"> </a></h3>
<p><strong>This class is deprecated and should always be avoided.</strong> It is intended to be used by code that runs on the logical server. Because localization should rarely happen on the server, other alternatives should be considered.</p>
<ul>
<li><code>translateToLocal(String)</code> localizes in the game&rsquo;s locale without formatting. The parameter is a translation key.</li>
<li><code>translateToLocalFormatted(String, Object...)</code> localizes in the game&rsquo;s locale with formatting. The first parameter is a translation key, and the rest are used as formatting arguments for <code>String.format(String, Object...)</code>.</li>
</ul>
<h3 id="textcomponenttranslation"><code>TextComponentTranslation</code><a class="headerlink" href="#textcomponenttranslation" title="Permanent link"> </a></h3>
<p><code>TextComponentTranslation</code> is an <code>ITextComponent</code> that is localized and formatted lazily. It is very useful when sending messages to players because it will be automatically localized in their own locale.</p>
<p>The first parameter of the <code>TextComponentTranslation(String, Object...)</code> constructor is a translation key, and the rest are used for formatting. The only supported format specifiers are <code>%s</code> and <code>%1$s</code>, <code>%2$s</code>, <code>%3$s</code> etc. Formatting arguments may be other <code>ITextComponent</code>s that will be inserted into the resulting formatted text with all their attributes preserved.</p>
<h3 id="textcomponenthelper"><code>TextComponentHelper</code><a class="headerlink" href="#textcomponenthelper" title="Permanent link"> </a></h3>
<ul>
<li><code>createComponentTranslation(ICommandSender, String, Object...)</code> creates a localized and formatted <code>ITextComponent</code> depending on a receiver. The localization and formatting is done eagerly if the receiver is a vanilla client. If not, the localization and formatting is done lazily with a <code>TextComponentTranslation</code>. This is only useful if the server should allow vanilla clients to connect.</li>
</ul>
</article>