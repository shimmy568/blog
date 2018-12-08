---
title: Top 5 Reasons Why You Should Consider VSCode
date: 2018-12-07 14:31:29
tags:
    - vscode
    - review
    - list
---

## Introduction

For those of you who live under a rock (Patrick star style) VSCode is the hot new open source text editor on the market for programmers made by Microsoft. Similar to Atom it's made in electron, while this may be a turn off to some people I've had no issues with ram consumption or speed yet so It's a bit of a non-issue for me. If you've ever used Visual Studio don't worry, Visual Studio Code only is related to it by name. It's a lightweight extendable IDE somewhere between sublime and full-fledged IDE in terms of features. Not to mention you can even configure these settings on a language by language basis for your user.

I'm going to try to avoid talking about plugins in this post since all editors can go crazy with plugins and be amazing. I just want to focus what you get out of the box with the editor.

## Reason 1 - Text-based Settings Files

This may not seem like a huge deal but honestly, it is. All the settings that you can configure in the editor change a settings.json file either in the workspace you are in or on a global scope. This means that you can have your editor settings stored in version control. So this means that if you set your editor to use 2 spaces for tabs, linter configuration or formatter style anyone else who opens your project with VSCode will have the exact same settings. This not only makes it really easy to work in teams in a professional setting but it lets you configure your editor on a project by project basis. Just a note but this same settings file also holds all the settings for all your plugins which if you didn't know is a huge part of what makes VSCode so awesome. 

![Workspace Settings For C++ Project](/assets/images/vscode/pic1.png)

Also, check our the [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync&WT.mc_id=vscode-smashing-buhollan) plugin for the ability to sync your settings with a GitHub Gist.

## Reason 2 - Active Open Source Development

VSCode is always coming out with new features on a monthly-ish basis. This means that you are constantly getting new features like a colour selector for CSS files or being able to pipe input from the command line into your current VSCode window. This means that the editor just keeps getting better and better over time. But this doesn't mean that it's not stable any change that they make doesn't break any extensions or config files that you have. 

The whole open source thing is pretty nice too it's actually up on GitHub right now [here](https://github.com/Microsoft/vscode). This means that if anyone has an issue it gets fixed quickly and push out in the next update. Hell if you wanted to fix the issue you found yourself you could not that it's a reasonable thing to do or anything. 

## Reason 3 - Amazing Git Integration

Git is the most popular version control system today. While VSCode supports other version control systems like SVN or Mercurial I'm going to focus on Git here since it's my choice of poison. Heres a short list of what the built-in version control systems in VSCode can do:

- All the standard git shit, commit, push, pull, branch, merge, checkout, etc
- Resolve merge conflicts in an easy to use format: [picture](/assets/images/vscode/pic2.png)
- Manage many sub-modules in a single repository: [picture](/assets/images/vscode/pic3.png)
- View diffs (pretty straightforward): [picture](/assets/images/vscode/pic4.png)

That doesn't even talk about the awesome git based plugins like [Git History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory), [Git Blame](https://marketplace.visualstudio.com/items?itemName=waderyan.gitblame), [Git Project Manager](https://marketplace.visualstudio.com/items?itemName=felipecaputo.git-project-manager), and [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) that turn your editor into a super-powered text editing git machine.

## Reason 4 - Built In Debugger

Ah, the good ole `console.log()` debugging, as awesome as it is (sarcasm) VSCode has its own debugger built in with multi-language support when combined with the language plugins. This makes it really easy to debug code large or small. It's saved my ass more times than I can count whether I'm doing an assignment in Java or a quick web app for a hackathon in Node. The support could be better for some stuff not gonna lie like you're not really able to use STDIN when using it. But that's about the only issue I've ever had with it. It has all the good shit like conditional breakpoints, stack trace, statement analysis and being able to view scoped variables. 

![Debugging a Java Project](/assets/images/vscode/pic5.png)

## Reason 5 - Extensions

Ok, so I lied we gonna talk about extensions. But I mean come on, these extensions are amazing, there are all your favourite themes, file icon themes, support for every language on the face of the earth, and more than I could talk about in a short article. The editor has been built from the ground up with extensions in mind, even the choice to go with electron was to make it as easy as possible for people to make extensions since lots of people know JavaScript and it's comparatively easy to pick up. Not to mention each extension can have its settings in that settings file I mention earlier and any feature the extension adds can be bound to a shortcut or just used through the command pallet. Pretty Nice

## The End

There are tons of editors out there from Vim to Notepad++, a lot of it comes down to preference and these are just a couple reasons I've always kept coming back to VSCode even after trying to pick up others, it just has the best support for my developing needs in my opinion. Or at least I haven't found anything that does it better yet when I do I'll be saying goodbye to VSCode, but I have a feeling it's going to be a while.
