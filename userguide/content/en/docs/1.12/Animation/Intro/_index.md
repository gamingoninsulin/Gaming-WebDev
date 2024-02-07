---
linkTitle: Intro
---

<article class="docs-entry">
<h1 id="intro-to-the-animation-api">Intro to the Animation API<a class="headerlink" href="#intro-to-the-animation-api" title="Permanent link"> </a></h1>
<p>The Forge Animation API lets you animate JSON (and B3D) models.
Before you start reading this, you should know how vanilla JSON models are created, and should have
read the documentation on blockstates.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Although you can use B3D models with the Animation API, most of this documentation will assume you
are using JSON files. (TODO) See the page on using B3D models for more information</p>
</div>
<p>The Animation API is made up of two main components: armature files and animation state machine (ASM) files.
Armature files define joints and clips for JSON files. Joints are names for cubes in the model file with weights (see the <a href="../armature/index.htm">page on armatures</a> for more info), while
clips are a set of transformations to apply to joints over time (think of a clip from a movie for example). ASM files define the various states an animation can be in, as well as what
transitions exist between those states. They also define the parameters for an animation (functions which return a floating point number), which are generally used as inputs to clips, but can also
trigger events. Events are essentially a way to receive in-code notifications when the animation reaches a certain point or to trigger transitions.</p>
<h2 id="filesystem-structure-conventions">Filesystem Structure Conventions<a class="headerlink" href="#filesystem-structure-conventions" title="Permanent link"> </a></h2>
<p>ASM Files are normally stored in the <code>asms/block</code> for blocks or <code>asms/item</code> for items and so on. You specify where to load
 them from, so their location is really up to you.</p>
<p>Armature files <em>must</em> be stored in the <code>armatures</code> folder. They are looked up by taking the path to your model file, removing <code>models/</code> and taking whats left and prepending
 <code>armatures/</code>, so a model in <code>models/block/test.json</code> becomes <code>armatures/block/test.json</code>.</p>
</article>