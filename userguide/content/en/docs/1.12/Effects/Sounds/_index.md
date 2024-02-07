---
linkTitle: Sounds
---

<article class="docs-entry">
<h1 id="sounds">Sounds<a class="headerlink" href="#sounds" title="Permanent link"> </a></h1>
<h2 id="terminology">Terminology<a class="headerlink" href="#terminology" title="Permanent link"> </a></h2>
<table>
<thead>
<tr>
<th>Term</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>Sound Events</td>
<td>Something that triggers a sound effect. Examples include <code>"minecraft:block.anvil.hit"</code> or <code>"botania:spreaderFire"</code>.</td>
</tr>
<tr>
<td>Sound Category</td>
<td>The category of the sound, for example <code>"player"</code>, <code>"block"</code> or simply <code>"master"</code>. The sliders in the sound settings GUI represent these categories.</td>
</tr>
<tr>
<td>Sound File</td>
<td>The literal file on disk that is played, usually an .ogg file.</td>
</tr>
</tbody>
</table>
<h2 id="soundsjson"><code>sounds.json</code><a class="headerlink" href="#soundsjson" title="Permanent link"> </a></h2>
<p>This JSON defines sound events, and defines which sound files they play, the subtitle, etc. Sound events are identified with <a href="../../concepts/resources/index.htm#resourcelocation"><code>ResourceLocation</code></a>s. <code>sounds.json</code> should be located at the root of a resource namespace (<code>assets/&lt;namespace&gt;/sounds.json</code>), and it defines sound events in that namespace (<code>assets/&lt;namespace&gt;/sounds.json</code> defines sound events in the namespace <code>namespace</code>.).</p>
<p>A full specification is available on the vanilla <a href="https://minecraft.gamepedia.com/Sounds.json">wiki</a>, but this example highlights the important parts:</p>
<pre class="highlight"><code class="language-json">{
  "open_chest": {
    "category": "block",
    "subtitle": "mymod.subtitle.openChest",
    "sounds": [ "mymod:open_chest_sound_file" ]
  },
  "epic_music": {
    "category": "record",
    "sounds": [
      {
        "name": "mymod:music/epic_music",
        "stream": true
      }
    ]
  }
}</code></pre>

<p>Underneath the top-level object, each key corresponds to a sound event. Note that the namespace is not given, as it is taken from the namespace of the JSON itself. Each event specifies its category, and a localization key to be shown when subtitles are enabled. Finally, the actual sound files to be played are specified. Note that the value is an array; if multiple sound files are specified then the game will randomly choose one to play whenever the sound event is triggered.</p>
<p>The two examples represent two different ways to specify a sound file. The <a href="https://minecraft.gamepedia.com/Sounds.json">wiki</a> has precise details, but generally, long sound files such as BGM or music discs should use the second form, because the &ldquo;stream&rdquo; argument tells Minecraft to not load the entire sound file into memory but to stream it from disk. The second form can also specify the volume, pitch, and random weight of a sound file.</p>
<p>In all cases, the path to a sound file for namespace <code>namespace</code> and path <code>path</code> is <code>assets/&lt;namespace&gt;/sounds/&lt;path&gt;.ogg</code>. Therefore <code>mymod:open_chest_sound_file</code> points to <code>assets/mymod/sounds/open_chest_sound_file.ogg</code>, and <code>mymod:music/epic_music</code> points to <code>assets/mymod/sounds/music/epic_music.ogg</code>.</p>
<h2 id="creating-sound-events">Creating Sound Events<a class="headerlink" href="#creating-sound-events" title="Permanent link"> </a></h2>
<p>In order to actually be able to play sounds, a <code>SoundEvent</code> corresponding to an entry in <code>sounds.json</code> must be created. This <code>SoundEvent</code> must then be <a href="../../concepts/registries/index.htm#registering-things">registered</a>. Normally, the location used to create a sound event should be set as it&rsquo;s registry name.</p>
<p>Creating a <code>SoundEvent</code>:</p>
<pre class="highlight"><code class="language-java">ResourceLocation location = new ResourceLocation("mymod", "open_chest");
SoundEvent event = new SoundEvent(location);</code></pre>

<p>The <code>SoundEvent</code> acts as a reference to the sound, and is passed around to actually play sounds. Therefore, the <code>SoundEvent</code> should be stored somewhere. If a mod has an API, it should expose its <code>SoundEvent</code>s in the API.</p>
<h2 id="playing-sounds">Playing Sounds<a class="headerlink" href="#playing-sounds" title="Permanent link"> </a></h2>
<p>Vanilla has lots of methods for playing sounds, and it&rsquo;s unclear which to use at times.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>This information was gathered by looking at these various methods, analyzing their usage and categorizing them accordingly. It is up-to-date as of Forge 1907, please let someone know if it is out of date!</p>
</div>
<p>Note that each takes a <code>SoundEvent</code>, the ones registered above. Additionally, the terms <em>&ldquo;Server Behavior&rdquo;</em> and <em>&ldquo;Client Behavior&rdquo;</em> refer to the respective <a href="../../concepts/sides/index.htm"><strong>logical</strong> side</a>.</p>
<h3 id="world"><code>World</code><a class="headerlink" href="#world" title="Permanent link"> </a></h3>
<ol>
<li>
<p><a name="world-playsound-pbecvp"></a> <code>playSound(EntityPlayer, BlockPos, SoundEvent, SoundCategory, volume, pitch)</code></p>
<ul>
<li>Simply forwards to <a href="#world-playsound-pxyzecvp">overload (2)</a>, adding 0.5 to each coordinate of the <code>BlockPos</code> given.</li>
</ul>
</li>
<li>
<p><a name="world-playsound-pxyzecvp"></a> <code>playSound(EntityPlayer, double x, double y, double z, SoundEvent, SoundCategory, volume, pitch)</code></p>
<ul>
<li><strong>Client Behavior</strong>: If the passed in player is <em>the</em> client player, plays the sound event to the client player.</li>
<li><strong>Server Behavior</strong>: Plays the sound event to everyone nearby <strong>except</strong> the passed in player. Player can be <code>null</code>.</li>
<li><strong>Usage</strong>: The correspondence between the behaviors implies that these two methods are to be called from some player-initiated code that will be run on both logical sides at the same time - the logical client handles playing it to the user and the logical server handles everyone else hearing it without re-playing it to the original user.
   They can also be used to play any sound in general at any position server-side by calling it on the logical server and passing in a <code>null</code> player, thus letting everyone hear it.</li>
</ul>
</li>
<li>
<p><a name="world-playsound-xyzecvpd"></a> <code>playSound(double x, double y, double z, SoundEvent, SoundCategory, volume, pitch, distanceDelay)</code></p>
<ul>
<li><strong>Client Behavior</strong>: Just plays the sound event in the client world. If <code>distanceDelay</code> is <code>true</code>, then delays the sound based on how far it is from the player.</li>
<li><strong>Server Behavior</strong>: Does nothing.</li>
<li><strong>Usage</strong>: This method only works client-side, and thus is useful for sounds sent in custom packets, or other client-only effect-type sounds. Used for thunder.</li>
</ul>
</li>
</ol>
<h3 id="worldclient"><code>WorldClient</code><a class="headerlink" href="#worldclient" title="Permanent link"> </a></h3>
<ol>
<li><a name="worldclient-playsound-becvpd"></a> <code>playSound(BlockPos, SoundEvent, SoundCategory, volume, pitch, distanceDelay)</code><ul>
<li>Simply forwards to <code>World</code>&lsquo;s <a href="#world-playsound-xyzecvpd">overload (3)</a>, adding 0.5 to each coordinate of the <code>BlockPos</code> given.</li>
</ul>
</li>
</ol>
<h3 id="entity"><code>Entity</code><a class="headerlink" href="#entity" title="Permanent link"> </a></h3>
<ol>
<li><a name="entity-playsound-evp"></a> <code>playSound(SoundEvent, volume, pitch)</code><ul>
<li>Forwards to <code>World</code>&lsquo;s <a href="#world-playsound-pxyzecvp">overload (2)</a>, passing in <code>null</code> as the player.</li>
<li><strong>Client Behavior</strong>: Does nothing.</li>
<li><strong>Server Behavior</strong>: Plays the sound event to everyone at this entity&rsquo;s position.</li>
<li><strong>Usage</strong>: Emitting any sound from any non-player entity server-side.</li>
</ul>
</li>
</ol>
<h3 id="entityplayer"><code>EntityPlayer</code><a class="headerlink" href="#entityplayer" title="Permanent link"> </a></h3>
<ol>
<li><a name="entityplayer-playsound-evp"></a> <code>playSound(SoundEvent, volume, pitch)</code> (overriding the one in <a href="#entity-playsound-evp"><code>Entity</code></a>)<ul>
<li>Forwards to <code>World</code>&lsquo;s <a href="#world-playsound-pxyzecvp">overload (2)</a>, passing in <code>this</code> as the player.</li>
<li><strong>Client Behavior</strong>: Does nothing, see override in <a href="#entityplayersp-playsound-evp"><code>EntityPlayerSP</code></a>.</li>
<li><strong>Server Behavior</strong>: Plays the sound to everyone nearby <em>except</em> this player.</li>
<li><strong>Usage</strong>: See <a href="#entityplayersp-playsound-evp"><code>EntityPlayerSP</code></a>.</li>
</ul>
</li>
</ol>
<h3 id="entityplayersp"><code>EntityPlayerSP</code><a class="headerlink" href="#entityplayersp" title="Permanent link"> </a></h3>
<ol>
<li><a name="entityplayersp-playsound-evp"></a> <code>playSound(SoundEvent, volume, pitch)</code> (overriding the one in <a href="#entityplayer-playsound-evp"><code>EntityPlayer</code></a>)<ul>
<li>Forwards to <code>World</code>&lsquo;s <a href="#world-playsound-pxyzecvp">overload (2)</a>, passing in <code>this</code> as the player.</li>
<li><strong>Client Behavior</strong>: Just plays the Sound Event.</li>
<li><strong>Server Behavior</strong>: Method is client-only.</li>
<li><strong>Usage</strong>: Just like the ones in <code>World</code>, these two overrides in the player classes seem to be for code that runs together on both sides. The client handles playing the sound to the user, while the server handles everyone else hearing it without re-playing to the original user.</li>
</ul>
</li>
</ol>
</article>