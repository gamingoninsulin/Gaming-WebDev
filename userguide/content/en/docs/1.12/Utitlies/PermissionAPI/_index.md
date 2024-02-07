---
linkTitle: PermissionAPI
---

<article class="docs-entry">
<h1 id="permissionapi">PermissionAPI<a class="headerlink" href="#permissionapi" title="Permanent link"> </a></h1>
<p>The PermissionAPI is a very basic implementation of a permission system.
Its default implementation doesn&rsquo;t add an advanced permission handling (like we know it for example from PEX), 
but instead it has 3 permission levels, (ALL = all players, OP = operators, NONE = neither normal players nor operators).
This behaviour can be changed by mods which implement their own PermissionHandler.</p>
<h2 id="how-to-use-the-permissionapi">How to use the PermissionAPI<a class="headerlink" href="#how-to-use-the-permissionapi" title="Permanent link"> </a></h2>
<p>For basic support you just need to call <code>PermissionAPI.hasPermission(EntityPlayer player, String  node)</code>,
though by default this is going to return always false, as the default implementation uses the permission level <code>NONE</code>
So if we want that all players, or just OP&rsquo;s to be able to use this  we also need to register our permission node.
Achieving this is as simple as checking for permissions: <code>PermissionAPI.registerNode(String node, DefaultPermissionLevel level, String description)</code>, 
though this has to be done in Init or Later.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>The PermissionAPI isn&rsquo;t restricted to be used for commands, you could also use it for other things, like restricting access to a GUI.</p>
</div>
<p>Also, you need to check if your <code>ICommandSender</code> is a player if you use it in combination with commands!</p>
<h2 id="defaultpermissionlevel"><code>DefaultPermissionLevel</code><a class="headerlink" href="#defaultpermissionlevel" title="Permanent link"> </a></h2>
<p>The <code>DefaultPermissionLevel</code> has 3 Values:
* ALL =all players got this permission
* OP = only operators got this permission
* NONE = neither normal players nor operators got this permission</p>
<h2 id="permission-node">Permission node<a class="headerlink" href="#permission-node" title="Permanent link"> </a></h2>
<p>While there are technically no rules for the permission nodes, the best practice is to be of the form <code>modid.subgroup.permission_id</code>
It is recommended to use this naming scheme as other implementations may have stricter rules.</p>
<h2 id="making-your-own-implementation-of-the-permissionhandler">Making your own implementation of the <code>PermissionHandler</code><a class="headerlink" href="#making-your-own-implementation-of-the-permissionhandler" title="Permanent link"> </a></h2>
<p>By default the PermissionHandler is very basic, which is usually enough for most users, 
but you might want more control over the permissions for things like a big server.
This can be achieved  by creating a custom <code>PermissionHandler</code>.</p>
<p>How it works and what is capable of, is totally up to you, for example you could make a simple implementation just saving a file per player.
Or you could make it as advanced as PEX, having database support and many other functions.</p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>Not every mod that wants to use the PermissionAPI should change the PermissionHandler as there can be only 1 at the same time!</p>
</div>
<p>First off, how you implement your own PermissionHandler is completely up to you, you can use files, a database or whatever you want.
All you need to do is create your own implementation of the interface <code>IPermissionHandler</code>.
After that is done, you also need to register it using:  <code>PermissionAPI.setPermissionHandler(IPermissionHandler handler)</code></p>
<div class="admonition note">
<p class="admonition-title">Note</p>
<p>You&rsquo;ve got to set the Handler during PreInit!
It is also recommended to check if it wasn&rsquo;t already replaced by another mod.</p>
</div>
</article>