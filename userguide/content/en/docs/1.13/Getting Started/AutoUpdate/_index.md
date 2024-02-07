---
linkTitle: AutoUpdate
---

<article class="docs-entry">
<h1 id="forge-update-checker">Forge Update Checker<a class="headerlink" href="#forge-update-checker" title="Permanent link"> </a></h1>
<p>Forge provides a very lightweight opt-in update-checking framework. All it does is check for updates, then show a flashing icon on the Mods button of the main menu and mod list if any mods have an available update, along with the respective changelogs. It <em>does not</em> download updates automatically.</p>
<h2 id="getting-started">Getting Started<a class="headerlink" href="#getting-started" title="Permanent link"> </a></h2>
<p>The first thing you want to do is specify the <code>updateJSON</code> parameter in your <code>@Mod</code> annotation. The value of this parameter should be a valid URL pointing to an update JSON file. This file can be hosted on your own web server, or on GitHub, or wherever you want, as long as it can be reliably reached by all users of your mod.</p>
<h2 id="update-json-format">Update JSON format<a class="headerlink" href="#update-json-format" title="Permanent link"> </a></h2>
<p>The JSON itself has a relatively simple format, given as follows:</p>
<pre class="highlight"><code class="language-Javascript">{
  "homepage": "&lt;homepage/download page for your mod&gt;",
  "&lt;mcversion&gt;": {
    "&lt;modversion&gt;": "&lt;changelog for this version&gt;", 
    // List all versions of your mod for the given Minecraft version, along with their changelogs
    ...
  },
  ...
  "promos": {
    "&lt;mcversion&gt;-latest": "&lt;modversion&gt;",
    // Declare the latest "bleeding-edge" version of your mod for the given Minecraft version
    "&lt;mcversion&gt;-recommended": "&lt;modversion&gt;",
    // Declare the latest "stable" version of your mod for the given Minecraft version
    ...
  }
}</code></pre>

<p>This is fairly self-explanatory, but some notes:</p>
<ul>
<li>The link under <code>homepage</code> is the link the user will be shown when the mod is outdated.</li>
<li>Forge uses an internal algorithm to determine whether one version String of your mod is &ldquo;newer&rdquo; than another. Most versioning schemes should be compatible, but see the <code>ComparableVersion</code> class if you are concerned about whether your scheme is supported. Adherence to <a href="https://semver.org/">semantic versioning</a> is highly recommended.</li>
<li>The changelog string can be separated into lines using <code>\n</code>. Some prefer to include a abbreviated changelog, then link to an external site that provides a full listing of changes.</li>
<li>Manually inputting data can be chore. You can configure your <code>build.gradle</code> to automatically update this file when building a release, as Groovy has native JSON parsing support. Doing this is left as an exercise to the reader.</li>
</ul>
<p>Two concrete examples can be seen here for <a href="https://gist.githubusercontent.com/Meow-J/fe740e287c2881d3bf2341a62a7ce770/raw/bf829cdefc84344d86d1922e2667778112b845b1/update.json">Charset</a> and <a href="https://gist.githubusercontent.com/Meow-J/1299068c775c2b174632534a18b65fb8/raw/42c578cf2303aa76d8900f5fdc6366122549d2a8/update.json">Botania Unofficial</a>.</p>
<h2 id="retrieving-update-check-results">Retrieving Update Check Results<a class="headerlink" href="#retrieving-update-check-results" title="Permanent link"> </a></h2>
<p>You can retrieve the results of the Forge Update Checker using <code>ForgeVersion.getResult(ModContainer)</code>. The returned object has a field <code>status</code> which indicates the status of the version check.
Example values: <code>FAILED</code> (the version checker couldn&rsquo;t connect to the URL provided), <code>UP_TO_DATE</code> (the current version is equal to or newer than the latest stable version), <code>OUTDATED</code> (there is a new stable version), <code>BETA_OUTDATED</code> (there is a new unstable version), or <code>BETA</code> (the current version is equal to or newer than the latest unstable version). The status will be <code>PENDING</code> if the result requested has not finished yet; in that case, you should try again in a little bit. 
Otherwise, the returned object will also have the target version and any changelog lines, as specified in <code>update.json</code>.
You can obtain your own <code>ModContainer</code> to pass to this method using <code>Loader.instance().activeModContainer()</code>, or any other mod&rsquo;s <code>ModContainer</code> using <code>Loader.instance().getIndexedModList().get(&lt;modid&gt;)</code>.</p>
</article>