---
linkTitle: Getting Started
---

<article class="docs-entry">
<h1 id="getting-started-with-forge">Getting Started with Forge<a class="headerlink" href="#getting-started-with-forge" title="Permanent link"> </a></h1>
<p>This is a simple guide to get you from nothing to a basic mod. The rest of this documentation is about where to go from here.</p>
<h2 id="from-zero-to-modding">From Zero to Modding<a class="headerlink" href="#from-zero-to-modding" title="Permanent link"> </a></h2>
<ol>
<li>Obtain a source distribution from forge&rsquo;s [files][] site. (Look for the MDK file type)</li>
<li>Extract the downloaded source distribution to an empty directory. You should see a bunch of files, and an example mod is placed in <code>src/main/java</code> for you to look at. Only a few of these files are strictly necessary for mod development, and you may reuse these files for all your projects These files are:<ul>
<li><code>build.gradle</code></li>
<li><code>gradlew.bat</code></li>
<li><code>gradlew</code></li>
<li>the <code>gradle</code> folder</li>
</ul>
</li>
<li>Move the files listed above to a new folder, this will be your mod project folder.</li>
<li>Choose your IDE:
* For both Intellij IDEA and Eclipse their Gradle integration will handle the rest of the initial workspace setup, this includes downloading packages from Mojang, MinecraftForge, and a few other software sharing sites.<ul>
<li>For most, if not all, changes to the build.gradle file to take effect Gradle will need to be invoked to re-evaluate the project, this can be done through Refresh buttons in the Gradle panels of both the previously mentioned IDEs.</li>
</ul>
</li>
</ol>
<h2 id="customizing-your-mod-information">Customizing Your Mod Information<a class="headerlink" href="#customizing-your-mod-information" title="Permanent link"> </a></h2>
<p>Edit the <code>build.gradle</code> file to customize how your mod is built (the file names, versions, and other things).</p>
<div class="admonition important">
<p class="admonition-title">Important</p>
<p><strong>Do not</strong> edit the <code>buildscript {}</code> section of the build.gradle file, its default text is necessary for ForgeGradle to function.</p>
</div>
<p>Almost anything underneath <code>apply project: forge</code> and the <code>// EDITS GO BELOW HERE</code> marker can be changed, many things can be removed and customized there as well.</p>
<p>There is a whole site dedicated to customizing the forge <code>build.gradle</code> files - the <a href="https://forgegradle.readthedocs.org/en/latest/cookbook/" title="The ForgeGradle cookbook">ForgeGradle cookbook</a>. Once you&rsquo;re comfortable with your mod setup, you&rsquo;ll find many useful recipes there.</p>
<h3 id="simple-buildgradle-customizations">Simple <code>build.gradle</code> Customizations<a class="headerlink" href="#simple-buildgradle-customizations" title="Permanent link"> </a></h3>
<p>These customizations are highly recommended for all projects.</p>
<ul>
<li>To change the name of the file you build - edit the value of <code>archivesBaseName</code> to suit.</li>
<li>To change your &ldquo;maven coordinates&rdquo; - edit the value of <code>group</code> as well.</li>
<li>To change the version number - edit the value of <code>version</code>.</li>
</ul>
<h2 id="building-and-testing-your-mod">Building and Testing Your Mod<a class="headerlink" href="#building-and-testing-your-mod" title="Permanent link"> </a></h2>
<ol>
<li>To build your mod, run <code>gradlew build</code>. This will output a file in <code>build/libs</code> with the name <code>[archivesBaseName]-[version].jar</code>. This file can be placed in the <code>mods</code> folder of a forge enabled Minecraft setup, and distributed.</li>
<li>To test run with your mod, the easiest way is to use the run configs that were generated when you set up your project. Otherwise, you can run <code>gradlew runClient</code>. This will launch Minecraft from the <code>&lt;runDir&gt;</code> location, including your mod code. There are various customizations to this command. Consult the <a href="https://forgegradle.readthedocs.org/en/latest/cookbook/" title="The ForgeGradle cookbook">ForgeGradle cookbook</a> for more information.</li>
<li>You can also run a dedicated server using the server run config, or <code>gradlew runServer</code>. This will launch the Minecraft server with it&rsquo;s GUI.</li>
</ol>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>It is always advisable to test your mod in a dedicated server environment if it is intended to run there.</p>
</div>
</article>