---
linkTitle: Versioning
---

<article class="docs-entry">
<h1 id="versioning">Versioning<a class="headerlink" href="#versioning" title="Permanent link"> </a></h1>
<p>In general projects, <a href="../../../../index-2.htm">Semantic Versioning</a> is often used (which has the format <code>MAJOR.MINOR.PATCH</code>). However, in the case of modding it may be more beneficial to use the format <code>MCVERSION-MAJORMOD.MAJORAPI.MINOR.PATCH</code>, to be able to differentiate between world-breaking and API-breaking changes of a mod.</p>
<h2 id="examples">Examples<a class="headerlink" href="#examples" title="Permanent link"> </a></h2>
<p>Here is a (most likely incomplete) list of things that increment the various variables.</p>
<ul>
<li><code>MCVERSION</code><ul>
<li>Always matches the Minecraft version the mod is for.</li>
</ul>
</li>
<li><code>MAJORMOD</code><ul>
<li>Removing items, blocks, tile entities, etc.</li>
<li>Changing or removing previously existing mechanics.</li>
<li>Updating to a new Minecraft version.</li>
</ul>
</li>
<li><code>MAJORAPI</code><ul>
<li>Changing the order or variables of enums.</li>
<li>Changing return types of methods.</li>
<li>Removing public methods altogether.</li>
</ul>
</li>
<li><code>MINOR</code><ul>
<li>Adding items, blocks, tile entities, etc.</li>
<li>Adding new mechanics.</li>
<li>Deprecating public methods. (This is not a <code>MAJORAPI</code> increment since it doesn&rsquo;t break an API.)</li>
</ul>
</li>
<li><code>PATCH</code><ul>
<li>Bugfixes.</li>
</ul>
</li>
</ul>
<p>When incrementing any variable, all lesser variables should reset to <code>0</code>. For instance, if <code>MINOR</code> would increment, <code>PATCH</code> would become <code>0</code>. If <code>MAJORMOD</code> would increment, all other variables would become <code>0</code>.</p>
<h3 id="work-in-progress">Work In Progress<a class="headerlink" href="#work-in-progress" title="Permanent link"> </a></h3>
<p>If you are in the initial development stage of your mod (before any official releases), the <code>MAJORMOD</code> and <code>MAJORAPI</code> should always be <code>0</code>. Only <code>MINOR</code> should be updated every time you build your mod. Once you build an official release (most of the time with a stable API), you should increment <code>MAJORMOD</code> to version <code>1.0.0.0</code>. For any further development stages, refer to the <a href="#prereleases">Prereleases</a> and <a href="#release-candidates">Release candidates</a> section of this document.</p>
<h3 id="multiple-minecraft-versions">Multiple Minecraft Versions<a class="headerlink" href="#multiple-minecraft-versions" title="Permanent link"> </a></h3>
<p>If the mod upgrades to a new version of Minecraft, and the old version will only receive bug fixes, the <code>PATCH</code> variable should be updated based on the version before the upgrade. If the mod is still in active development in both the old and the new version of Minecraft, it is advised to append the version to <strong>both</strong> build numbers. For example, if the mod is upgraded to version <code>3.0.0.0</code> due to a Minecraft version change, the old mod should also be updated to <code>3.0.0.0</code>. The old version will become, for example, version <code>1.7.10-3.0.0.0</code>, while the new version will become <code>1.8-3.0.0.0</code>. If there are no changes at all when building for a newer Minecraft version, all variables except for the Minecraft version should stay the same.</p>
<h3 id="final-release">Final Release<a class="headerlink" href="#final-release" title="Permanent link"> </a></h3>
<p>When dropping support for a Minecraft version, the last build for that version should get the <code>-final</code> suffix. This denotes that the mod will no longer be supported for the denoted <code>MCVERSION</code> and that players should upgrade to a newer version of the mod to continue receiving updates and bug fixes.</p>
<h3 id="pre-releases">Pre-releases<a class="headerlink" href="#pre-releases" title="Permanent link"> </a></h3>
<p>It is also possible to prerelease work-in-progress features, which means new features are released that are not quite done yet. These can be seen as a sort of &ldquo;beta&rdquo;. These versions should be appended with <code>-betaX</code>, where X is the number of the prerelease. (This guide does not use <code>-pre</code> since, at the time of writing, it is not a valid alias for <code>-beta</code> according to Forge.) Note that already released versions and versions before the initial release can not go into prerelease; variables (mostly <code>MINOR</code>, but <code>MAJORAPI</code> and <code>MAJORMOD</code> can also prerelease) should be updated accordingly before adding the <code>-beta</code> suffix. Versions before the initial release are simply work-in-progress builds.</p>
<h3 id="release-candidates">Release Candidates<a class="headerlink" href="#release-candidates" title="Permanent link"> </a></h3>
<p>Release candidates act as prereleases before an actual version change. These versions should be appended with <code>-rcX</code>, where <code>X</code> is the number of the release candidate which should, in theory, only be increased for bugfixes. Already released versions can not receive release candidates; variables (mostly <code>MINOR</code>, but <code>MAJORAPI</code> and <code>MAJORMOD</code> can also prerelease)  should be updated accordingly before adding the <code>-rc</code> suffix. When releasing a release candidate as stable build, it can either be exactly the same as the last release candidate or have a few more bug fixes.</p>
</article>