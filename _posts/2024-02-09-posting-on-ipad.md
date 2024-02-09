---
layout: post
title:  "Posting on iPad"
date:   2024-02-09 12:00:00 +0000
categories: 
    - project
    - editing
    - git
    - iPad
---

I’m travelling at the moment & with an aim to pack light took just the iPad. As I’ve got a bit of free time I thought I’d finally get round to updating the site. It’s hosted on GitHub pages and I use git to manage the site. My usual tools on are VSCode as a markdown editor and the git command line. I thought it’d be a good opportunity to try out [vscode.dev](https://vscode.dev) in Safari to see how it works. Logging in using my GitHub credentials allowed me to easily checkout the website’s code, but the editing experience left something to be desired. VSCode takes over the tap and hold gesture used to allow you to select text & replaces it with its own right click menu. This seems a sensible choice, but I could not get copy and pasting a block of text to work correctly. With a keyboard and mouse plugged in this might make a decent setup but again travelling light, I’m stuck with just the onscreen keyboard. Giving up on VSCode I looked around for a native markdown editor & came across Koder. It’s basic but functional, however it lacks built in git support, it does allow reading files from the iPads local storage however. ish came to the rescue. It is a Alpine Linux container that allows CLI tools like git to be used on your iPad. My workflow has become:

* Checkout the repo in ish
* Use Files to copy the project from ish to koder
* Make any additions and edits in koder
* Copy back to ish & git commit/push
* Use the GitHub app to check the PR and merge

If you’re seeing this the process works! Not the smoothest, but functional in a pinch.