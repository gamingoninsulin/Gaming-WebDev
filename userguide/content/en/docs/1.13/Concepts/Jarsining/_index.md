---
linkTitle: Jarsining
---

<article class="docs-entry">
<h1 id="signing-a-jar">Signing a JAR<a class="headerlink" href="#signing-a-jar" title="Permanent link"> </a></h1>
<p>Java allows developers to sign their jar files with a signature. However, these signatures are not designed 
to be used as security and shouldn&rsquo;t be used this way. Signatures are used as sanity checks, so that developers
are able to check if they are running their own un-edited code.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Once again keep in mind that this system is not intended to be a security measure. With enough malicious intend it can be circumvented.</p>
</div>
<h2 id="creating-a-keystore">Creating a keystore<a class="headerlink" href="#creating-a-keystore" title="Permanent link"> </a></h2>
<p>A <strong>keystore</strong> is a private database file which holds the required information to successfully sign the jar.
To sign a jar, you need both a public and a private key. The public key will be later used to check if the
jar is correctly signed.</p>
<p>To generate a key, execute the following command and follow the instructions given by the keytool.
The key should be <strong>SHA-1 encoded</strong>.
<pre class="highlight"><code class="language-shell">keytool -genkey -alias signFiles -keystore examplestore.jks</code></pre>
* The <code>keytool</code> is part of the Java Development Kit and can be found in the underlying <code>bin</code> directory.
* The <code>alias signFiles</code> indicates that the alias should be used in future to refer to the keystore entry
* <code>-keystore examplestore.jks</code> means that the keystore will be saved to the file <code>examplestore.jks</code>.
<h3 id="get-the-public-key">Get the public key<a class="headerlink" href="#get-the-public-key" title="Permanent link"> </a></h3>
<p>To gather the public key required by Forge, execute the following command:
<pre class="highlight"><code class="language-shell">keytool -list -alias signFiles -keystore examplestore.jks</code></pre>
Now copy the public key and remove all colons and change all uppercase letters to lowercase to ensure
that FML can work with the key.
<h3 id="checking-at-runtime">Checking at runtime<a class="headerlink" href="#checking-at-runtime" title="Permanent link"> </a></h3>
<p>To allow FML to compare the keys, add the public key to the <code>certificateFingerprint</code> argument in the <code>@Mod</code> annotation.</p>
<p>When FML detects that the keys don&rsquo;t match it will fire the mod-lifecycle event <code>FMLFingerprintViolationEvent</code> How to 
handle this key mismatch is up to the developer.</p>
<ul>
<li><code>event.isDirectory()</code> - Returns true when the mod runs in development environment.</li>
<li><code>event.getSource()</code> - Returns the file with the key mismatch.</li>
<li><code>event.getExpectedFingerprint()</code> - Returns the public key.</li>
<li><code>event.getFingerprints()</code> - Returns all public keys found.</li>
</ul>
<h2 id="buildscript-setup">Buildscript setup<a class="headerlink" href="#buildscript-setup" title="Permanent link"> </a></h2>
<p>Finally, to let Gradle sign the jar file with the generated key pair, a new task in the
buildscript <code>build.gradle</code> is required.</p>
<pre class="highlight"><code class="language-groovy">    task signJar(type: SignJar, dependsOn: reobfJar) {
        inputFile = jar.archivePath
        outputFile = jar.archivePath
    }

    build.dependsOn signJar</code></pre>

<ul>
<li><code>dependsOn: reobfJar</code> - This snippet of the line is important because Gradle has to sign the jar <strong>after</strong> ForgeGradle has reobfuscated the jar.</li>
<li><code>jar.archivePath</code> - The path where the archive (jar) is constructed.</li>
<li><code>build.dependsOn signJar</code> - This line tells Gradle that this task is part of the build task started by <code>gradlew build</code>.</li>
</ul>
<p>The following values for the <code>signJar</code> task must be defined to ensure that Gradle can find the keystore and actually sign the jar.
This has to be done in the <code>gradle.properties</code> file.</p>
<ul>
<li><code>keyStore</code> - This value tells Gradle where to search for the generated keystore.</li>
<li><code>alias</code> - The alias which was defined in the above is required in order for Gradle to sign the jar.</li>
<li><code>storePass</code> - The password which was defined by creating the keystore is required in order for Gradle to access the file.</li>
<li><code>keyPass</code> - The password of the key which was defined by creating the keystore is required in order for Gradle to gain access to it. This password may be the same as storePass, but can be different.</li>
</ul>
</article>