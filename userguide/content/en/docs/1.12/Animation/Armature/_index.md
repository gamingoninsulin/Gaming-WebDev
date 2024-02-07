---
linkTitle: Armature
---

<article class="docs-entry">
<h1 id="armature-files">Armature Files<a class="headerlink" href="#armature-files" title="Permanent link"> </a></h1>
                <p>Armature Files define joints and clips for animating a model.</p>
                <h2 id="file-structure">File Structure<a class="headerlink" href="#file-structure" title="Permanent link"> </a></h2>
                <p>An example armature file, taken from the forge debug mod
                <pre class="highlight"><code class="language-json">{
    "joints": {
        "stick": {"2": [1.0]},
        "cube": {"3": [1.0]}
    },
    "clips": {
        "default": {
        "loop": true,
        "joint_clips": {
            "stick": [
            {
                "variable": "offset_x",
                "type": "uniform",
                "interpolation": "linear",
                "samples": [0, 0.6875, 0]
            }
            ],
            "cube": [
            {
                "variable": "offset_x",
                "type": "uniform",
                "interpolation": "linear",
                "samples": [0, 0.6875, 0]
            },
            {
                "variable": "axis_z",
                "type": "uniform",
                "interpolation": "nearest",
                "samples": [ 1 ]
            },
            {
                "variable": "origin_x",
                "type": "uniform",
                "interpolation": "nearest",
                "samples": [ 0.15625 ]
            },
            {
                "variable": "origin_y",
                "type": "uniform",
                "interpolation": "nearest",
                "samples": [ 0.40625 ]
            },
            {
                "variable": "angle",
                "type": "uniform",
                "interpolation": "linear",
                "samples": [0, 120, 240, 0, 120, 240]
            }
        ]
        },
        "events": {}
        }
    }
}
</code></pre>
                <p>The file is organized in two sections, joints and clips.</p>
                <h2 id="joints">Joints<a class="headerlink" href="#joints" title="Permanent link"> </a></h2>
                <p>Each joint defines an object that animates together as a single unit in the animation. With Vanilla JSON models, this means that multiple elements can belong to the same joint.</p>
                <p>The format is like this:
                <pre class="highlight"><code class="language-javascript"> {
    "joints": {
        &lt;joint&gt;, ...
    }
}
---
&lt;joint&gt; ::= {
    &lt;string&gt;: {  // joint name
        &lt;joint_definition&gt;, ...
    }
}
&lt;joint_definition&gt; ::= {
    "&lt;index_model&gt;": [ &lt;float&gt; ] // index_model, joint_weight (only one value expected in array)
}
</code></pre>
                <ul>
                    <li><code>joint_name</code> is the name of the joint</li>
                    <li><code>index_model</code> is a 0-indexed number (where 0 is the first element defined in the model) denoting a model element this joint controls. Must be a string (see example)</li>
                    <li><code>joint_weight</code> is a weight (0-1) of how much this joint will contribute to the element&rsquo;s final transformation if the element is used in multiple joints.</li>
                </ul>
                <div class="admonition note">
                    <p class="admonition-title">Note</p>
                    <p>For simpler animations, the weight can usually just be set to 1.0, but if you want multiple joints in a clip to animate differently, this is one way to accomplish that.</p>
                </div>
                <p>Not all elements need to have a joint, only the ones you are animating. If an element occurs in multiple joint, the final transform is a weighted average of the transforms for each joint.</p>
                <h2 id="clips">Clips<a class="headerlink" href="#clips" title="Permanent link"> </a></h2>
                <p>Clips are essentially instructions on how to use a value to animate some collection of joints. They also include events to fire at certain points.</p>
                <p>They are formatted like this:
                <pre class="highlight"><code class="language-javascript">
{
    "clips": {
        "clip_name": {
            "loop": &lt;true/false&gt;,
            "joint_clips": {
                &lt;joint_clip_list&gt;, ...
            },
            "events": {
                &lt;event&gt; ...
            }
        }
    }
}
-------
&lt;joint_clip_list&gt; ::= {
    "joint_name": [
        &lt;joint_clip&gt;, ...
    ]
}&lt;joint_clip&gt; ::= {
    "variable": &lt;variable&gt;,
    "type": "uniform",
    "interpolation": &lt;interpolation&gt;,
    "samples": [ float, ... ]
}
</code></pre>
                <ul>
                    <li>loop: if true, the animation will wrap around when the parameter value goes above 1, if not it&rsquo;ll just clamp at the final state.</li>
                </ul>
                <h3 id="joint-clips">Joint Clips<a class="headerlink" href="#joint-clips" title="Permanent link"> </a> </h3>
                <p>Each <code>joint_clip</code> is a set of variables to change for a joint. The <code>type</code> attribute is currently ignored, but must be <code>"uniform"</code>.</p>
                <p><code>samples</code> defines what value the animation will take on (think of keyframes in traditional animation), and its interpretation depends on the value of <code>interpolation</code>.</p>
                <p><code>interpolation</code>, which is how to convert the list of samples into a (possibly) continuous animation can be one of the following:</p>
                <ul>
                    <li><code>nearest</code> - if value &lt; 0.5 use the first sample, else the second sample. Useful for static variables if only given one value.</li>
                    <li><code>linear</code> - linearly interpolate between samples. Time between samples is 1 / number of samples.</li>
                </ul>
                <p><code>variable</code> can be one of the following:</p>
                <ul>
                    <li><code>offset_x</code>, <code>offset_y</code>, <code>offset_z</code> - translation</li>
                    <li><code>scale</code> - uniform scaling</li>
                    <li><code>scale_x</code>, <code>scale_y</code>, <code>scale_x</code> - scaling on certain axes</li>
                    <li><code>axis_x</code>, <code>axis_y</code>, <code>axis_z</code> - rotation axes</li>
                    <li><code>angle</code> - rotation angle</li>
                    <li><code>origin_x</code>, <code>origin_y</code>, <code>origin_z</code> - rotation origin</li>
                </ul>
                <h3 id="events">Events<a class="headerlink" href="#events" title="Permanent link"> </a></h3>
                <p>Each clip can fire events, formatted like this:
                <pre class="highlight"><code class="language-javascript">&lt;event&gt; :: {
    &lt;event_time&gt;: "event_text"
}</code></pre> For more information about events and what <code>event_text</code> means, see the page on <a href="../asm/index.htm">ASMs</a>. <p><code>event_time</code> is a value (usually between 0 and 1 inclusive) denoting when to fire the event. When the parameter controlling this clip reaches a point equal to or greater than the <code>event_time</code>, the event is fired.</p>
</article>