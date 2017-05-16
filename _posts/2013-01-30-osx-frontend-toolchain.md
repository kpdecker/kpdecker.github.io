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

### Editors

- [SublimeText 2](http://www.sublimetext.com/)

  Common plugins include:

  - Package Control - Allows all other plugins to be installed
  - BracketHighlighter
  - Git
  - Handlebars.tmBundle
  - JsFormat
  - JsHint
  - PrettyJSON
  - Stylus
  - SublimeSaveOnBuild
  - TrailingSpaces


  Useful config options:

  ```json
  {
    "bold_folder_labels": true,
    "draw_white_space": "all",
    "ensure_newline_at_eof_on_save": true,
    "file_exclude_patterns":
    [
      "*.tmproj",
      "*.sublime-workspace",
      "*.class",
      ".DS_Store"
    ],
    "folder_exclude_patterns":
    [
      ".svn",
      ".git",
      ".hg",
      "CVS",
      "node_modules"
    ],
    "highlight_line": true,
    "highlight_modified_tabs": true,
    "rulers":
    [
      100
    ],
    "tab_size": 2,
    "translate_tabs_to_spaces": true
  }
  ```

### SCM

- [Git Command Line](http://git-scm.com/)

  Your mileage may vary but by far the strongest git client out there.

  Most common git commands

  - `git checkout -b $newBranchName`
  - `git stash`
  -  `git pull --rebase` (Warn do not do after a branch merge)

  Ensure that your version of git has completions enabled (this might require [manual installation](https://gist.github.com/972430))

  Customizing your bash prompt for git status is also very helpful:

  This script whose origin has been lost will output the current branch and change status in your bash profile.

  ```bash
  function parse_git_branch {
    git branch --no-color 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
  }
  function last_two_dirs {
    pwd |rev| awk -F / '{print $1,$2}' | rev | sed s_\ _/_
  }

  c_cyan=`tput setaf 6`
  c_red=`tput setaf 1`
  c_green=`tput setaf 2`
  c_sgr0=`tput sgr0`

  function proml {
    PS1='\h:$(last_two_dirs)\[$(branch_color)\]$(parse_git_branch)\[${c_sgr0}\] \u\$ '
  }
  function branch_color() {
    if git rev-parse --git-dir >/dev/null 2>&1
    then
      color=""
      git diff --quiet 2>/dev/null >&2
      if [[ $? -eq 0 ]]
      then
        color=${c_cyan}
      else
        color=${c_red}
      fi
    else
      return 0
    fi
    echo -ne $color
  }

  proml
  ```

- [Gitx](http://rowanj.github.com/gitx/)

  GUI frontend on various commit related git operations. The staging view is very valuable for doing interactive commits and self-code review at commit time.

- [SourceTree](http://www.sourcetreeapp.com/)

  General git repository viewer. Helpful for viewing stashed changes that may have accumulated on your local tree.

- [git-extras](https://github.com/visionmedia/git-extras)

  A collection of useful tools for doing higher level git commands such as `delete-branch`. When installing make sure that the command line tab completion tools to no error out as these are very helpful.

### Debugging and Optimizing

- [Charles](http://www.charlesproxy.com/)

  HTTP proxy and debugger with easy to use interface.

- [xScope](http://iconfactory.com/software/xscope)

  Inspect/measure mocks and final rendered pages.

- [Gradient Scanner](http://www.gradient-scanner.com/)

  (Shameless plug) Extract CSS gradients from flatted mock images.

- [ImageAlpha/ImageOptim](http://pngmini.com/)

  Image optimization tools. Performs both lossless and lossy compression of image assets.

### Documents

- [Dropbox](https://www.dropbox.com/)
  
  Easy way to backup remotely and share content across the team.

- [Evernote](http://evernote.com/)

  Free notetaking service.

- MS Office

  Because somethings you can't escape.

- [MS Remote Desktop Client](http://www.microsoft.com/mac/remote-desktop-client)

  Again somethings you can not escape.


### Communication

- [Adium](http://adium.im/)

  Chat over many protocols. Most common among the team are GTalk and AIM.

- [Propane](http://propaneapp.com/)

  Team chat. Setup growl notifications and stay connected.

- [Skype](http://beta.skype.com/en/)

  Sometimes you need to use your voice. Horrible at IM.

- [ScreenFlow](http://www.telestream.net/screenflow/overview.htm)

  Generate screen casts for sharing content with coworkers or debugging transient behaviors.

### System

- [Alfred](http://www.alfredapp.com/)

  Search-based system launcher.

- [Growl](http://growl.info/)

  Common notification system for Lumbar builds and Propane and others.

- [Divvy](http://mizage.com/divvy/)

  Window position manager

- [Stay](http://cordlessdog.com/stay/)

  Utility which will restore your windows when you connect or disconnect a monitor.

- [BetterTouchTool](http://www.boastr.de/)

  Keyboard/mouse/touchpad gesture mapper.

- [Cloud App](http://getcloudapp.com/)

  Dirt simple screenshot sharing app. Useful for sharing screenshots and other local content over IM and email when attachments might not be supported or supported easily.

- [Homebrew](http://mxcl.github.com/homebrew/)

  OSS package manager.

- [nvm](https://github.com/creationix/nvm)

  Node version switcher. Easily allows for switching between different versions of node.

  Current recommendations for the latest 0.8 node branch for running the development build stack as the watch issues on the 0.8 branch seem to have been resolved.