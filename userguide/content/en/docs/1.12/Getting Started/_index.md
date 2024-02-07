---
linkTitle: Getting Started
---
<article class="docs-entry">
               <h1 id="getting-started-with-forge">Getting Started with Forge<a class="headerlink" href="#getting-started-with-forge" title="Permanent link"> </a></h1>
               <p>This is a simple guide to get you from nothing to a basic mod. The rest of this documentation is about where to go from here.</p>
               <h2 id="from-zero-to-modding">From Zero to Modding<a class="headerlink" href="#from-zero-to-modding" title="Permanent link"> </a></h2>
               <ol>
                  <li>Obtain a source distribution from forge&rsquo;s <a href="../../../net/minecraftforge/forge/index.htm" title="Forge Files distribution site">files</a> site. (Look for the Mdk file type, or Src in older 1.8/1.7 versions).</li>
                  <li>
                     Extract the downloaded source distribution to an empty directory. You should see a bunch of files, and an example mod is placed in <code>src/main/java</code> for you to look at. Only a few of these files are strictly necessary for mod development, and you may reuse these files for all your projects These files are:
                     <ul>
                        <li><code>build.gradle</code></li>
                        <li><code>gradlew.bat</code></li>
                        <li><code>gradlew</code></li>
                        <li>the <code>gradle</code> folder</li>
                     </ul>
                  </li>
                  <li>Move the files listed above to a new folder, this will be your mod project folder.</li>
                  <li>Open up a command prompt in the folder you created in step (3), then run <code>gradlew setupDecompWorkspace</code>. This will download a bunch of artifacts from the internet needed to decompile and build Minecraft and forge. This might take some time, as it will download stuff and then decompile Minecraft. Note that, in general, these things will only need to be downloaded and decompiled once, unless you delete the gradle artifact cache.</li>
                  <li>
                     Choose your IDE: Forge explicitly supports developing with Eclipse or IntelliJ environments, but any environment, from Netbeans to vi/emacs, can be made to work.
                     <ul>
                        <li>For Eclipse, you should run <code>gradlew eclipse</code> - this will download some more artifacts for building eclipse projects and then place the eclipse project artifacts in your current directory.</li>
                        <li>For IntelliJ, simply import the build.gradle file.</li>
                     </ul>
                  </li>
                  <li>
                     Load your project into your IDE.
                     <ul>
                        <li>For Eclipse, create a workspace anywhere (though the easiest location is one level above your project folder). Then simply import your project folder as a project, everything will be done automatically.</li>
                        <li>For IntelliJ, you only need to create run configs. You can run <code>gradlew genIntellijRuns</code> to do this.</li>
                     </ul>
                  </li>
               </ol>
               <div class="admonition note">
                  <p class="admonition-title">Note</p>
                  <p>In case you will receive an error while running the task <code>:decompileMC</code> ( the fourth step )</p>
                  <pre class="highlight"><code>Execution failed for task ':decompileMc'.
GC overhead limit exceeded</code></pre>
                  <p>assign more RAM into gradle by adding <code>org.gradle.jvmargs=-Xmx2G</code> into the file <code>~/.gradle/gradle.properties</code> (create file if doesn&rsquo;t exist). The <code>~</code> sign means it&rsquo;s a user&rsquo;s <a href="../../../wiki/Home_directory.html#Default_home_directory_per_operating_system" title="Default user's home folder location for different operation systems">home directory</a>    .</p>
               </div>
               <h2 id="terminal-free-intellij-idea-configuration">Terminal-free IntelliJ IDEA configuration<a class="headerlink" href="#terminal-free-intellij-idea-configuration" title="Permanent link"> </a></h2>
               <p>These instructions assume that you have created the project folder as described in the steps 1 to 3 of the section above. Because of that, the numbering starts at 4.</p>
               <ol start="4">
                  <li>Launch IDEA and choose to open/import the <code>build.gradle</code> file, using the default gradle wrapper choice. While you wait for this process to finish, you can open the gradle panel, which will get filled with the gradle tasks once importing is completed.</li>
                  <li>Run the <code>setupDecompWorkspace</code> task (inside the <code>forgegradle</code> task group). It will take a few minutes, and use quite a bit of RAM. If it fails, you can add <code>-Xmx3G</code> to the <code>Gradle VM options</code> in IDEA&rsquo;s gradle settings window, or edit your global gradle properties.</li>
                  <li>Once the setup task is done, you will want to run the <code>genIntellijRuns</code> task, which will configure the project&rsquo;s run/debug targets. </li>
                  <li>After it&rsquo;s done, you should click the blue refresh icon <strong>on the gradle panel</strong> (there&rsquo;s another refresh icon on the main toolbar, but that&rsquo;s not it). This will re-synchronize the IDEA project with the Gradle data, making sure that all the dependencies and settings are up to date.</li>
                  <li>Finally, assuming you use IDEA 2016 or newer, you will have to fix the classpath module. Go to <code>Edit configurations</code> and in both <code>Minecraft Client</code> and <code>Minecraft Server</code>, change <code>Use classpath of module</code> to point to the task with a name like <code>&lt;project&gt;_main</code>.</li>
               </ol>
               <p>If all the steps worked correctly, you should now be able to choose the Minecraft run tasks from the dropdown, and then click the Run/Debug buttons to test your setup.</p>
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
                  <li>You can also run a dedicated server using the server run config, or <code>gradlew runServer</code>. This will launch the Minecraft server with its GUI.</li>
               </ol>
               <div class="admonition note">
                  <p class="admonition-title">Note</p>
                  <p>It is always advisable to test your mod in a dedicated server environment if it is intended to run there.</p>
               </div>
            </article>

