---
linkTitle: Debug Profiler
---

<article class="docs-entry">
<h1 id="debug-profiler">Debug Profiler<a class="headerlink" href="#debug-profiler" title="Permanent link"> </a></h1>
<p>Minecraft provides a Debug Profiler that can be used to find time consuming code. Specially considering things like <code>TickEvents</code> and Ticking <code>TileEntities</code> this can be very useful for modders and server owners that want to find a lag source.</p>
<h2 id="using-the-debug-profiler">Using the Debug Profiler<a class="headerlink" href="#using-the-debug-profiler" title="Permanent link"> </a></h2>
<p>The Debug Profiler is very simple to use. It requires two commands <code>/debug start</code>, which starts the profiling process, and <code>/debug stop</code>, which ends it.
The important part here is that the more time you give it to collect the data the better the results will be.
It is recommended to at least let it collect data for a minute.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
</div>
<p>Naturally, you can only profile code paths that are actually being reached. Entities and TileEntities that you want to profile must exist in the world to show up in the results.</p>
<p>After you&rsquo;ve stopped the debugger it will create a new file, it can be found within the <code>debug</code> subdirectory in your run directory.
The file name will be formatted with the date and time as <code>profile-results-yyyy-mm-dd_hh.mi.ss.txt</code></p>
<h2 id="reading-a-profiling-result">Reading a Profiling result<a class="headerlink" href="#reading-a-profiling-result" title="Permanent link"> </a></h2>
<p>At the top it first tells you how long in milliseconds it was running and how many ticks ran in that time.</p>
<p>Below that, you will find information similar to the snippet below:
<pre class="highlight"><code>[00] levels - 96.70%/96.70%
[01] |   World Name - 99.76%/96.47%
[02] |   |   tick - 99.31%/95.81%
[03] |   |   |   entities - 47.72%/45.72%
[04] |   |   |   |   regular - 98.32%/44.95%
[04] |   |   |   |   blockEntities - 0.90%/0.41%
[05] |   |   |   |   |   unspecified - 64.26%/0.26%
[05] |   |   |   |   |   minecraft:furnace - 33.35%/0.14%
[05] |   |   |   |   |   minecraft:chest - 2.39%/0.01%</code></pre>
Here is a small explanation of what each part means
<table>
<thead>
<tr>
<th align="left">[02]</th>
<th align="left">tick</th>
<th align="left">99.31%</th>
<th align="left">95.81%</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">The Depth of the section</td>
<td align="left">The Name of the Section</td>
<td align="left">The percentage of time it took in relation to it&rsquo;s parent. For Layer 0 it&rsquo;s the percentage of the time a tick takes, while for Layer 1 it&rsquo;s the percentage of the time its parent takes</td>
<td align="left">The second Percentage tells you how much Time it took from the entire tick.</td>
</tr>
</tbody>
</table>
<h2 id="profiling-your-own-code">Profiling your own code<a class="headerlink" href="#profiling-your-own-code" title="Permanent link"> </a></h2>
<p>The Debug Profiler has basic support for <code>Entity</code> and <code>TileEntity</code>. If you would like to profile something else, you may need to manually create your sections like so:
<pre class="highlight"><code class="language-JAVA">  Profiler#startSection(yourSectionName : String);
  //The code you want to profile
  Profiler#endSection();</code></pre>
You can obtain the the <code>Profiler</code> instance from a <code>World</code>, <code>MinecraftServer</code>, or <code>Minecraft</code> instance.
Now you just need to search the File for your section name.
</article>