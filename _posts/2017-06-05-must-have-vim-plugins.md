---
id: 324
title: Must-have vim plugins
date: 2017-06-05T11:53:30+00:00
author: Mahdi M.
layout: post
guid: http://www.mahdix.com/?p=324
permalink: /blog/2017/06/05/must-have-vim-plugins/
medium_post:
  - 'O:11:"Medium_Post":11:{s:16:"author_image_url";s:68:"https://cdn-images-1.medium.com/fit/c/200/200/0*a70L__x5V0qVwdcB.jpg";s:10:"author_url";s:26:"https://medium.com/@mahdix";s:11:"byline_name";N;s:12:"byline_email";N;s:10:"cross_link";s:3:"yes";s:2:"id";s:12:"eaef6add492b";s:21:"follower_notification";s:3:"yes";s:7:"license";s:19:"all-rights-reserved";s:14:"publication_id";s:2:"-1";s:6:"status";s:6:"public";s:3:"url";s:61:"https://medium.com/@mahdix/must-have-vim-plugins-eaef6add492b";}'
categories:
  - Uncategorized
---
The other day I was cleaning up my .vimrc file and thought about writing a post about plugins that I use and the reason.

I believe in simplicity, especially in my work environment. That&#8217;s why I always try to customize as little as possible and try to use existing features. During past couple of years since I started using vim, I have found below plugins really useful.

### [Vundle](https://github.com/VundleVim/Vundle.vim)

This is the most important plugin that I use. It helps my install other plugins more easily and conveniently.

You just need to start your \`.vimrc\` file with specific format and then for each plugin you want to have, add a line in this format:

`Plugin 'tpope/vim-commentary'`

The text inside single quote is the GitHub address of the repository of the plugin (except github.com).

But I think having so few plugins, I may be able to get rid of this one in future.

### [vim-commentary](https://github.com/tpope/vim-commentary)

This is the plugin which helps me comment/uncomment a line of a block of text. You simply need to use \`gc\` and the currently selected block will be commented (or commented out if it&#8217;s already commented). \`gcc\` does this for the current line.

You can combine this command with text movement verbs (e.g. \`gc10j\` to comment current and next 10 lines).

The good thing about this plugin is that it detects the correct commenting syntax so it doesn&#8217;t matter if you are working on a bash script or Python source code file. Just press the shortcut and this plugin will handle the rest.

### [fzf](https://github.com/junegunn/fzf.vim)

I am always editing files and switching between buffers in vim. Unfortunately, standard features of vim are never enough for me (I wonder why they don&#8217;t provide better options built-in). That&#8217;s why I use this excellent plugin.

It uses \`fzf\` command line utility as a back-end and lets you search for a buffer, file, MRU or text in current or all buffers. It is a pretty handy tool for me.

## Plugins that I used before but not now

### [supertab](https://github.com/ervandew/supertab)

This is a great plugin which lets you use <TAB> key in insert mode to invoke autocomplete feature of vim. But I find current shortcuts enough for my job, especially considering the fact that, due to vim being a text editor, I cannot expect any sophisticated autocomplete which makes this feature less useful for me.

### [CtrlP](https://github.com/kien/ctrlp.vim)

This was the plugin I was using before fzf. It&#8217;s a very powerful plugin and lets you search for buffer, file, text and some other handy features.

The reason I switched to fzf was that it uses fzf command line utility which can be handy sometimes for me when I am working inside the terminal.

### [mru](https://github.com/vim-scripts/mru.vim)

In a (failed) effort to replace CtrlP with a simpler alternative, I tried this one but gave up and returned to my favorite searcher plugin.

This is a useful plugin which helps search into most recently opened files and open them.

### [vim-buffergator](https://github.com/jeetsukumaran/vim-buffergator)

Another plugin which was supposed to replace fzf (CtrlP at that time) but did not succeed. It lets you search in current open buffers and switch to another buffer.

### [delimitMate](https://github.com/Raimondi/delimitMate)

This plugin provides autocompletion for delimiters like single-quote, double-quote, braces, etc.

The reason I no longer use this plugin is that during my edits, there are lots of times that I need to insert a single delimiter, but this plugin comes into my way and forces insertion of an extra delimiter which I have to delete. Sometimes this can be frustrating, although some other times it can be handy.

### [vim-operator-highlight](https://github.com/Valloric/vim-operator-highlight)

This plugin highlights all operators (\`()+-*/\` and other similar operators) in your file. This can help you read a source code file more easily but my problem with this plugin was that it also highlights comment section of the code which makes reading comments annoying. Also having a lot of different colors in your text editor can sometimes be confusing.

### [syntastic](https://github.com/vim-syntastic/syntastic)

This plugin checks the syntax of the current source code file and lets you know the error messages (if any). It highlights error lines and lets you easily navigate between errors. Personally, I prefer to use a terminal to check/compile my source code. And also having experience in working with dynamic typing languages, there is not much use for syntax checking as almost everything is evaluated at runtime (which I don&#8217;t like but that&#8217;s another story).