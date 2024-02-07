---
linkTitle: Dependency Management
---

<article class="docs-entry">
<h1 id="dependency-management">Dependency Management<a class="headerlink" href="#dependency-management" title="Permanent link"> </a></h1>
<p>Forge has some support for managing and loading mod dependencies. Libraries, and even other mods, can be embedded in builds in a way that lets Forge extract and load them at runtime in a compatible manner.</p>
<h2 id="the-mod-repository">The mod repository<a class="headerlink" href="#the-mod-repository" title="Permanent link"> </a></h2>
<p>The mod repository is a Maven-like repository containing mods and libraries. An artifact in this repository is identified by its Maven coordinate: <code>groupId:artifactId:version:classifier@extention</code>. Classifer and extension are optional. Forge can archive, manage and load mods and libraries in this repository. The mod repository may contain multiple versions of mods and libraries, including snapshot versions.</p>
<p>Forge can archive a jar in the repository if its <code>Maven-Artifact</code> manifest attribute is defined. The value of this attribute should be its Maven coordinate.</p>
<p>The mod repository supports snapshot artifacts. If the artifact version ends with <code>-SNAPSHOT</code>, the artifact will be resolved as the version with the latest timestamp. The timestamp can be set as the <code>Timestamp</code> manifest attribute, which should be the time since the epoch in milliseconds.</p>
<h2 id="dependency-extraction">Dependency extraction<a class="headerlink" href="#dependency-extraction" title="Permanent link"> </a></h2>
<p>Forge provides a simple way to embed dependencies in a mod and have them extracted at runtime. By placing dependency jars in your own jar, Forge can extract them into the mod repository and load them. This can be used as an alternative to shading, and comes with the potential benefit of resolving dependency version conflicts.</p>
<p>The contained dependencies of a jar file are marked by the <code>ContainedDeps</code> manifest attribute. Its value should be a space separated list of the names of contained jar files that will be extracted. These jar files should be placed in <code>/META-INF/libraries/{entry}</code>.</p>
<p>Forge will inspect the manifest of the contained jar to determine its Maven coordinate so that it may be archived. If a file <code>/META-INF/libraries/{entry}.meta</code> exists, Forge will read this as the jar&rsquo;s manifest instead. The dependency will be archived in the local repository according to its <code>Maven-Artifact</code> manifest attribute.</p>
</article>