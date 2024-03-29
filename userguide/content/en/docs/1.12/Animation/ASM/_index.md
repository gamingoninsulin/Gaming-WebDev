---
linkTitle: ASM
---

<article class="docs-entry">
<h1 id="animation-state-machine-files">Animation State Machine Files<a class="headerlink" href="#animation-state-machine-files" title="Permanent link"> </a></h1>
<p>Animation State Machine (ASM) Files are the meat of the animation API. These define how the animation is carried out and how to use the clips defined in the armature file.</p>
<h2 id="concepts">Concepts<a class="headerlink" href="#concepts" title="Permanent link"> </a></h2>
<p>The ASM contains <em>parameters</em>, <em>clips</em>, <em>states</em>, and <em>transitions</em>.</p>
<h3 id="states">States<a class="headerlink" href="#states" title="Permanent link"> </a></h3>
<p>The Animation <em>State</em> Machine can be in many different <em>states</em>. You define which states there are in the states section.</p>
<h3 id="transitions">Transitions<a class="headerlink" href="#transitions" title="Permanent link"> </a></h3>
<p>Transistions define which states are allowed to go to other states, for example allowing a <code>closed</code> state to go to an <code>open</code> state.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Transitions do <em>not</em> define animations that are played between states, however. If you want to do that you must create an additional state
that plays an animation then uses an event to go to the next state.</p>
</div>
<h3 id="parameters">Parameters<a class="headerlink" href="#parameters" title="Permanent link"> </a></h3>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Parameters are called <code>TimeValues</code> in the code, hence the naming convention of SomethingValue.</p>
</div>
<p>All parameters take an input, usually the current game time in seconds as a float (factoring in partial ticks) and outputs another time. This output is then used as the input to a clip, telling it the
current progress of the animation.</p>
<p>Each parameter can either be defined in the ASM or when you load the ASM in the code. Load-time parameters are usually of the type <code>VariableValue</code>, which returns a value changeable in-code, ignoring its input.
Other types allow you to do math on the input (<code>SimpleExprValue</code>), return a constant (<code>ConstValue</code>), refer to other parameters (<code>ParameterValue</code>), return the
input unmodified (<code>IdentityValue</code>) and perform composition of two parameters (<code>CompositionValue</code>).</p>
<h3 id="clips">Clips<a class="headerlink" href="#clips" title="Permanent link"> </a></h3>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Clips can either be ASM-clips, ones that are defined in the ASM, or armature-clips, ones that are defined in the armature file.
For the rest of this page, &ldquo;clips&rdquo; will refer to ASM-clips unless otherwise stated.</p>
</div>
<p>A clip takes in an input, usually the time, and does something to the model with it. Different types of clips do different things, the simplest being animating an
armature-clip (<code>ModelClip</code>). You can also override the input to another ASM-clip (<code>TimeClip</code>), trigger an event while animating another clip if the input is positive (<code>TriggerClip</code>),
smoothly blend between two clips (<code>SlerpClip</code>), refer to another clip in the ASM (<code>ClipReference</code>) or do nothing (<code>IdentityClip</code>).</p>
<h3 id="events">Events<a class="headerlink" href="#events" title="Permanent link"> </a></h3>
<p>Various things can trigger events in the ASM. Events in the ASM are represented using only text.
Some events are special, with text that is formatted like this: <code>!event_type:event_value</code>. Right now there is only one kind of <code>event_type</code>, namely <code>transition</code>. This tries to transition to whatever state is defined in the <code>event_value</code>. Anything else is a normal event and can be used from the <code>pastEvents</code> callback, but more information about that is on the <a href="../implementing/index.htm">implementing</a> page.</p>
<h2 id="code-api">Code API<a class="headerlink" href="#code-api" title="Permanent link"> </a></h2>
<div class="admonition warning">
<p class="admonition-title">Warning</p>
<p>The ASM code API can only be used <em>client side</em>. When storing ASMs in code, use the side-agnostic <code>IAnimationStateMachine</code> interface.</p>
</div>
<p>ASMs can be loaded by calling <code>ModelLoaderRegistry.loadASM</code>. It takes two parameters, the first being a <code>ResourceLocation</code> denoting
where the ASM is stored, and second an ImmutableMap of load-time defined parameters.</p>
<p>An example:
<pre class="highlight"><code class="language-java">@Nullable
private final IAnimationStateMachine asm;
private final VariableValue cycle = new VariableValue(4);

public Spin() {
     asm = proxy.loadASM(new ResourceLocation(MODID, "asms/block/rotatest.json"), ImmutableMap.of("cycle_length", cycle));
}</code></pre>
<p>Here, an ASM is loaded (from a sidedproxy to avoid crashing on server) with one extra parameter, named <code>cycle_length</code>. This parameter is of the type <code>VariableValue</code>, so we can
set it from within our code.</p>
<p>Using an ASM instance, you can get the current state with <code>.currentState()</code> and transition to another state with <code>.transition(nextState)</code></p>
<p><code>VariableValue</code> parameters can have their value set by calling <code>.setValue</code>, but you can not read this value back. There is no need
to inform the ASM of this change, it happens automatically.</p>
<h2 id="file-format">File Format<a class="headerlink" href="#file-format" title="Permanent link"> </a></h2>
<p>The ASMs are stored in json files. The location does not matter, but they are usually placed in an <code>asms</code> folder.</p>
<p>First, a simple example:
<pre class="highlight"><code class="language-json">{
  "parameters": {
    "anim_cycle": ["/", "#cycle_length"]
  },
  "clips": {
    "default": ["apply", "forgedebugmodelanimation:block/rotatest@default", "#anim_cycle" ]
  },
  "states": [
    "default"
  ],
  "transitions": {},
  "start_state": "default"
}</code></pre>
<p>As stated above, the files have parameters, clips, states and transitions, as well as the starting state of the ASM.</p>
<p>All of these tags are required, even if they are empty.</p>
<h3 id="parameters_1">Parameters<a class="headerlink" href="#parameters_1" title="Permanent link"> </a></h3>
<pre class="highlight"><code class="language-javascript">{
    "name": &lt;parameter_definition&gt;
}</code></pre>

<p>Different types of parameters have different formats for <code>&lt;parameter_definition&gt;</code>, and the simple ones are:</p>
<ul>
<li><code>IdentityValue</code>: the string <code>#identity</code>,</li>
<li><code>ParameterValue</code>: the parameter to reference, prefixed with <code>#</code>, e.g. <code>#my_awesome_parameter</code></li>
<li><code>ConstValue</code>: a number to use as the constant to return</li>
</ul>
<h4 id="mathematical-expression-simpleexprvalue">Mathematical expression (<code>SimpleExprValue</code>)<a class="headerlink" href="#mathematical-expression-simpleexprvalue" title="Permanent link"> </a></h4>
<p>Format: <code>[ regex("[+\\-*/mMrRfF]+"), &lt;parameter_definition&gt;, ... ]</code></p>
<h5 id="examples">Examples:<a class="headerlink" href="#examples" title="Permanent link"> </a></h5>
<pre class="highlight"><code class="language-json">[ "+", 4 ]
[ "/+", 5, 1]
[ "++", 2, "#other" ]
[ "++", "#other", [ "compose", "#cycle", 3] ]</code></pre>

<h5 id="explanation">Explanation<a class="headerlink" href="#explanation" title="Permanent link"> </a></h5>
<p>The <code>SimpleExprValue</code> takes its input and applies operations to it.
The first parameter is the sequence of operations to apply, and the rest represent the operands to those operations. The
input to each operation is either the input to this entire parameter (for the first operation) or the result of the previous operation.</p>
<h5 id="operations-case-sensitive">Operations (case-sensitive):<a class="headerlink" href="#operations-case-sensitive" title="Permanent link"> </a></h5>
<table>
<thead>
<tr>
<th>Operator</th>
<th>Meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>+</code></td>
<td><code>output = input + arg</code></td>
</tr>
<tr>
<td><code>-</code></td>
<td><code>output = input - arg</code></td>
</tr>
<tr>
<td><code>*</code></td>
<td><code>output = input * arg</code></td>
</tr>
<tr>
<td><code>/</code></td>
<td><code>output = input / arg</code></td>
</tr>
<tr>
<td><code>m</code></td>
<td><code>output = min(input, arg)</code></td>
</tr>
<tr>
<td><code>M</code></td>
<td><code>output = max(input, arg)</code></td>
</tr>
<tr>
<td><code>r</code></td>
<td><code>output = floor(input / arg) * arg</code></td>
</tr>
<tr>
<td><code>R</code></td>
<td><code>output = ceil(input / arg) * arg</code></td>
</tr>
<tr>
<td><code>f</code></td>
<td><code>output = input - floor(input / arg) * arg</code></td>
</tr>
<tr>
<td><code>F</code></td>
<td><code>output = ceil(input / arg) * arg - input</code></td>
</tr>
</tbody>
</table>
<h5 id="example-explanations">Example explanations:<a class="headerlink" href="#example-explanations" title="Permanent link"> </a></h5>
<ul>
<li>input + 4</li>
<li>(input / 5) + 1</li>
<li>input + 2 + value of parameter <code>other</code></li>
<li>input + value of parameter <code>other</code> + value of parameter <code>cycle</code> given input 3</li>
</ul>
<h4 id="function-composition-compositionvalue">Function composition (<code>CompositionValue</code>)<a class="headerlink" href="#function-composition-compositionvalue" title="Permanent link"> </a></h4>
<p>Format: <code>[ "compose", &lt;parameter_definition&gt;, &lt;parameter_definition&gt; ]</code></p>
<h5 id="examples_1">Examples:<a class="headerlink" href="#examples_1" title="Permanent link"> </a></h5>
<pre class="highlight"><code class="language-json">[ "compose", "#cycle", 3]
[ "compose", "#test", "#other"]
[ "compose", [ "+", 3], "#other"]
[ "compose", [ "compose", "#other2", "#other3"], "#other"]</code></pre>

<h5 id="explanation_1">Explanation<a class="headerlink" href="#explanation_1" title="Permanent link"> </a></h5>
<p><code>CompositionValue</code> takes two parameter definitions as inputs, and does <code>value1(value2(input))</code>. In other words, it chains
the two inputs, calling the second one with the given input, and the first one with the output of the second one.</p>
<h5 id="example-explanations_1">Example explanations:<a class="headerlink" href="#example-explanations_1" title="Permanent link"> </a></h5>
<ul>
<li>value of parameter <code>cycle</code> when given input 3</li>
<li>value of parameter <code>test</code> when given the output of parameter other when called with the current input</li>
<li>3 + the output of other with the current <code>input</code></li>
<li><code>other2(other3(other(input)))</code> because <code>value1</code> = <code>other2(other3(input))</code> and <code>value2</code> = <code>other(input)</code></li>
</ul>
<h3 id="clips_1">Clips<a class="headerlink" href="#clips_1" title="Permanent link"> </a></h3>
<pre class="highlight"><code class="language-javascript">{
    "name": &lt;clip_definition&gt;
}</code></pre>

<p>As with parameters, different kinds of clips have different formats for <code>&lt;clip_definition&gt;</code>, but the simple ones are:</p>
<ul>
<li><code>IdentityClip</code>: the string <code>#identity</code></li>
<li><code>ClipReference</code>: the clip name prefixed with <code>#</code>, e.g. <code>#my_amazing_clip</code></li>
<li><code>ModelClip</code>: a model resource location + <code>@</code> + the name of the armature-clip, e.g. <code>mymod:block/test@default</code> or <code>mymod:block/test#facing=east@moving</code></li>
</ul>
<h4 id="overriding-input-timeclip">Overriding input (<code>TimeClip</code>)<a class="headerlink" href="#overriding-input-timeclip" title="Permanent link"> </a></h4>
<p>Format: <code>[ "apply", &lt;clip_definition&gt;, &lt;parameter_definition&gt; ]</code></p>
<h5 id="examples_2">Examples:<a class="headerlink" href="#examples_2" title="Permanent link"> </a></h5>
<pre class="highlight"><code class="language-json">["apply", "mymod:block/animated_thing@moving", "#cycle_time"]
["apply", [ "apply", "mymod:block/animated_thing@moving", [ "+", 3 ] ], "#cycle"]</code></pre>

<h5 id="explanation_2">Explanation<a class="headerlink" href="#explanation_2" title="Permanent link"> </a></h5>
<p>The <code>TimeClip</code> takes another clip and applies it using a custom parameter instead of the current time. Usually used to apply a <code>ModelClip</code> with
a parameter instead of the current time.</p>
<h5 id="example-explanations_2">Example explanations:<a class="headerlink" href="#example-explanations_2" title="Permanent link"> </a></h5>
<ul>
<li>apply the armature-clip for model <code>mymod:block/animated_thing</code> named moving with the output of the parameter <code>cycle_time</code></li>
<li>apply the armature-clip for model <code>mymod:block/animated_thing</code> named moving with 3 + the output of the parameter <code>cycle</code></li>
</ul>
<h4 id="triggering-an-event-triggerclip">Triggering an event (<code>TriggerClip</code>)<a class="headerlink" href="#triggering-an-event-triggerclip" title="Permanent link"> </a></h4>
<p>Format: <code>[ "trigger_positive", &lt;clip_definition&gt;, &lt;parameter_definition&gt;, "&lt;event_text&gt;"]</code></p>
<h5 id="examples_3">Examples<a class="headerlink" href="#examples_3" title="Permanent link"> </a></h5>
<pre class="highlight"><code class="language-json">[ "trigger_positive", "#default", "#end_cycle", "!transition:moving" ]
[ "trigger_positive", "mymod:block/animated_thing@moving", "#end_cycle", "boop" ] </code></pre>

<h5 id="explanation_3">Explanation<a class="headerlink" href="#explanation_3" title="Permanent link"> </a></h5>
<p>The <code>TriggerClip</code> visually acts as a <code>TimeClip</code>, but also fires the event in <code>event_text</code> when the <code>parameter_description</code> goes positive.
At the same time, it applies the clip in <code>clip_definition</code> with the same <code>parameter_description</code>.</p>
<h5 id="example-explanations_3">Example explanations<a class="headerlink" href="#example-explanations_3" title="Permanent link"> </a></h5>
<ul>
<li>apply the clip with name default given the input of parameter <code>end_cycle</code>, and when <code>end_cycle</code> is positive transition to the <code>moving</code> state</li>
<li>apply the armature-clip <code>mymod:block/animated_thing@moving</code> with parameter <code>end_cycle</code>, and when <code>end_cycle</code> is positive fire event <code>"boop"</code></li>
</ul>
<h4 id="blend-between-two-clips-slerpclip">Blend between two clips (<code>SlerpClip</code>)<a class="headerlink" href="#blend-between-two-clips-slerpclip" title="Permanent link"> </a></h4>
<p>Format: <code>[ "slerp", &lt;clip_definition&gt;, &lt;clip_definition&gt;, &lt;parameter_definition&gt;, &lt;parameter_definition&gt; ]</code></p>
<h5 id="examples_4">Examples<a class="headerlink" href="#examples_4" title="Permanent link"> </a></h5>
<pre class="highlight"><code class="language-json">[ "slerp", "#closed", "#open", "#identity", "#progress" ]
[ "slerp", [ "apply", "#move", "#mover"], "#end", "#identity", "#progress" ]</code></pre>

<h5 id="explanation_4">Explanation<a class="headerlink" href="#explanation_4" title="Permanent link"> </a></h5>
<p>The <code>SlerpClip</code> performs a spherical linear blend between two separate clips. In other words, it will morph one clip into another smoothly.
The two <code>clip_definition</code>s are the clips to blend from and to respectively. The first <code>parameter_definition</code> is the &ldquo;input&rdquo;. Both the from and to clips
are passed the output of this parameter with the current animation time. The second <code>parameter_definition</code> is the &ldquo;progress&rdquo;, a value between 0 and 1 to denote
how far into the blend we are. Combining this clip with trigger_positive and transition special events can allow for simple transitions between two solid states.</p>
<h6 id="example-explanations_4">Example explanations<a class="headerlink" href="#example-explanations_4" title="Permanent link"> </a></h6>
<ul>
<li>blend the closed clip to the open clip, giving both clips the unaltered time as input and blend progress <code>#progress</code>.</li>
<li>blend the result of the move clip when given the input parameter <code>mover</code> to the end clip with the unaltered time as the input with blend progress <code>#progress</code>.</li>
</ul>
<h3 id="states_1">States<a class="headerlink" href="#states_1" title="Permanent link"> </a></h3>
<p>The states section of the file is simply a list of all possible states.
For example
<pre class="highlight"><code class="language-json">"states": [
  "open",
  "closed",
  "opening",
  "closing",
  "dancing"
]</code></pre>
defines 5 states: open, closed, opening, closing and dancing.
<h2 id="transitions_1">Transitions<a class="headerlink" href="#transitions_1" title="Permanent link"> </a></h2>
<p>The transitions section defines which states can go to what other states. A state can go to 0, 1, or many other states.
To define a state as going to no other states, omit it from the section. To define a state as going to only one other state, create a key
with the value of the state it can go to, for example <code>"open": "opening"</code>. To define a state as going to many other states, do the same as
if it were going to only one other state but make the value a list of all possible recieving states instead, for example: <code>"open": ["closed", "opening"]</code>.</p>
<p>A more full example:</p>
<pre class="highlight"><code class="language-json">"transitions": {
  "open": "closing",
  "closed": [ "dancing", "opening" ],
  "closing": "closed",
  "opening": "open",
  "dancing": "closed"
}</code></pre>

<p>This example means that:</p>
<ul>
<li>the open state can go to the closing state</li>
<li>the closed state can go to either the dancing or opening state</li>
<li>the closing state can go to the closed state</li>
<li>the opening state can go to the open state</li>
<li>the dancing state can go to the closed state</li>
</ul>
</article>