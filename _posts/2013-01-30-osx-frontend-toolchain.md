---
layout: post
title: OSX Frontend Toolchain
date: 2013-01-30 01:08:10.000000000 -06:00
categories:
- Dev
tags:
- debugging
- Lumbar
- toolchain
- Web Development
---

I'm currently in the middle of inventorying my machine so I can retire the time machine image that has spanned far too many years and far too many machines. Rather than stuffing this in Evernote to be used once I figured I'd post this to the community.

What other frontend tools are missing? Which ones have better alternatives?

<article>

### Editors
<ul>
<li>
    <a href="http://www.sublimetext.com/">SublimeText 2</a></p>
<p>Common plugins include:</p>
<ul>
<li>Package Control - Allows all other plugins to be installed</li>
<li>BracketHighlighter</li>
<li>Git</li>
<li>Handlebars.tmBundle</li>
<li>JsFormat</li>
<li>JsHint</li>
<li>PrettyJSON</li>
<li>Stylus</li>
<li>SublimeSaveOnBuild</li>
<li>TrailingSpaces</li>
</ul>
<p>Useful config options:</p>
<p>[javascript]{<br />
  &quot;bold_folder_labels&quot;: true,<br />
  &quot;draw_white_space&quot;: &quot;all&quot;,<br />
  &quot;ensure_newline_at_eof_on_save&quot;: true,<br />
  &quot;file_exclude_patterns&quot;:<br />
  [<br />
    &quot;*.tmproj&quot;,<br />
    &quot;*.sublime-workspace&quot;,<br />
    &quot;*.class&quot;,<br />
    &quot;.DS_Store&quot;<br />
  ],<br />
  &quot;folder_exclude_patterns&quot;:<br />
  [<br />
    &quot;.svn&quot;,<br />
    &quot;.git&quot;,<br />
    &quot;.hg&quot;,<br />
    &quot;CVS&quot;,<br />
    &quot;node_modules&quot;<br />
  ],<br />
  &quot;highlight_line&quot;: true,<br />
  &quot;highlight_modified_tabs&quot;: true,<br />
  &quot;rulers&quot;:<br />
  [<br />
    100<br />
  ],<br />
  &quot;tab_size&quot;: 2,<br />
  &quot;translate_tabs_to_spaces&quot;: true<br />
}<br />
[/javascript]
</li>
</ul>
### SCM
<ul>
<li>
    <a href="http://git-scm.com/">Git Command Line</a></p>
<p>Your mileage may vary but by far the strongest git client out there.</p>
<p>Most common git commands</p>
<ul>
<li><code>git checkout -b $newBranchName</code></li>
<li><code>git stash</code></li>
<li>
    <code>git pull --rebase</code> (Warn do not do after a branch merge)</li>
</ul>
<p>Ensure that your version of git has completions enabled (this might require <a href="https://gist.github.com/972430">manual installation</a>)</p>
<p>Customizing your bash prompt for git status is also very helpful:</p>
<p>This script whose origin has been lost will output the current branch and change status in your bash<br />
    profile.</p>
<p>    [bash]<br />
function parse_git_branch {<br />
  git branch --no-color 2&gt; /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'<br />
}<br />
function last_two_dirs {<br />
  pwd |rev| awk -F / '{print $1,$2}' | rev | sed s_\ _/_<br />
}</p>
<p>c_cyan=`tput setaf 6`<br />
c_red=`tput setaf 1`<br />
c_green=`tput setaf 2`<br />
c_sgr0=`tput sgr0`</p>
<p>function proml {<br />
  PS1='\h:$(last_two_dirs)\[$(branch_color)\]$(parse_git_branch)\[${c_sgr0}\] \u\$ '<br />
}<br />
function branch_color() {<br />
  if git rev-parse --git-dir &gt;/dev/null 2&gt;&amp;1<br />
  then<br />
    color=&quot;&quot;<br />
    git diff --quiet 2&gt;/dev/null &gt;&amp;2<br />
    if [[ $? -eq 0 ]]<br />
    then<br />
      color=${c_cyan}<br />
    else<br />
      color=${c_red}<br />
    fi<br />
  else<br />
    return 0<br />
  fi<br />
  echo -ne $color<br />
}</p>
<p>proml<br />
    [/bash]
  </li>
<li>
    <a href="http://rowanj.github.com/gitx/">Gitx</a></p>
<p>GUI frontend on various commit related git operations. The staging view is very valuable for doing<br />
    interactive commits and self-code review at commit time.</p>
</li>
<li>
    <a href="http://www.sourcetreeapp.com/">SourceTree</a></p>
<p>General git repository viewer. Helpful for viewing stashed changes that may have accumulated on your<br />
    local tree.</p>
</li>
<li>
    <a href="https://github.com/visionmedia/git-extras">git-extras</a></p>
<p>A collection of useful tools for doing higher level git commands such as <code>delete-branch</code>. When<br />
    installing make sure that the command line tab completion tools to no error out as these are very<br />
    helpful.</p>
</li>
</ul>
### Debugging and Optimizing
<ul>
<li>
    <a href="http://www.charlesproxy.com/">Charles</a></p>
<p>HTTP proxy and debugger with easy to use interface.</p>
</li>
<li>
    <a href="http://iconfactory.com/software/xscope">xScope</a></p>
<p>Inspect/measure mocks and final rendered pages.</p>
</li>
<li>
    <a href="http://www.gradient-scanner.com/">Gradient Scanner</a></p>
<p>(Shameless plug) Extract CSS gradients from flatted mock images.</p>
</li>
<li>
    <a href="http://pngmini.com/">ImageAlpha/ImageOptim</a></p>
<p>Image optimization tools. Performs both lossless and lossy compression of image assets.</p>
</li>
</ul>
### Documents
<ul>
<li>
    <a href="https://www.dropbox.com/">Dropbox</a></p>
<p>Easy way to backup remotely and share content across the team.</p>
</li>
<li>
    <a href="http://evernote.com/">Evernote</a></p>
<p>Free notetaking service.</p>
</li>
<li>
    MS Office</p>
<p>Because somethings you can't escape.</p>
</li>
<li>
    <a href="http://www.microsoft.com/mac/remote-desktop-client">MS Remote Desktop Client</a></p>
<p>Again somethings you can not escape.</p>
</li>
</ul>
### Communication
<ul>
<li>
    <a href="http://adium.im/">Adium</a></p>
<p>Chat over many protocols. Most common among the team are GTalk and AIM.</p>
</li>
<li>
    <a href="http://propaneapp.com/">Propane</a></p>
<p>Team chat. Setup growl notifications and stay connected.</p>
</li>
<li>
    <a href="http://beta.skype.com/en/">Skype</a></p>
<p>Sometimes you need to use your voice. Horrible at IM.</p>
</li>
<li>
    <a href="http://www.telestream.net/screenflow/overview.htm">ScreenFlow</a></p>
<p>Generate screen casts for sharing content with coworkers or debugging transient behaviors.</p>
</li>
</ul>
### System
<ul>
<li>
    <a href="http://www.alfredapp.com/">Alfred</a></p>
<p>Search-based system launcher.</p>
</li>
<li>
    <a href="http://growl.info/">Growl</a></p>
<p>Common notification system for Lumbar builds and Propane and others.</p>
</li>
<li>
    <a href="http://mizage.com/divvy/">Divvy</a></p>
<p>Window position manager</p>
</li>
<li>
    <a href="http://cordlessdog.com/stay/">Stay</a></p>
<p>Utility which will restore your windows when you connect or disconnect a monitor.</p>
</li>
<li>
    <a href="http://www.boastr.de/">BetterTouchTool</a></p>
<p>Keyboard/mouse/touchpad gesture mapper.</p>
</li>
<li>
    <a href="http://getcloudapp.com/">Cloud App</a></p>
<p>Dirt simple screenshot sharing app. Useful for sharing screenshots and other local content over<br />
    IM and email when attachments might not be supported or supported easily.</p>
</li>
<li>
    <a href="http://mxcl.github.com/homebrew/">Homebrew</a></p>
<p>OSS package manager.</p>
</li>
<li>
    <a href="https://github.com/creationix/nvm">nvm</a></p>
<p>Node version switcher. Easily allows for switching between different versions of node.</p>
<p>Current recommendations for the latest 0.8 node branch for running the development build stack as the<br />
    watch issues on the 0.8 branch seem to have been resolved.</p>
</li>
</ul>
</article>
