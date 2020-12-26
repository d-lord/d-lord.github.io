---
title: "Zsh notes from the image2webp Quick Action"
date: 2020-12-26 00:00:00
published: true
excerpt: "Some notes on zsh environment variables while creating a macOS quick action to prepare images for web publishing."
thumbnail: /assets/images/resize_and_webp_source.png
tags: macos automator zsh bash
---

These are some notes from a custom script called "image2webp", which acts as a macOS Quick Action to prepare an image for publishing on my website. It takes an image and resizes it to some common display sizes, as well as producing versions in [WebP](https://en.wikipedia.org/wiki/WebP) format for browsers which support it.

<video src="https://lord.geek.nz/f/resize-and-webp-demo.mp4" style="max-width: 100%; width: 100%;" controls="true"></video>

As you can see, the script is built into Finder, and you can right-click any image to create a folder containing web-ready versions.

That's the demo. The rest of this post will discuss an issue with zsh and `$PATH`, and its possible solutions, in tedious technical detail.

# Full analysis for nerds

The action has two parts: a workflow using macOS' built-in Automator app, which shows up in Finder, and the shell script it calls upon to do the work.

Here's the workflow itself.

![Screenshot of the macOS Automator app, showing the workflow as a shell script.](/assets/images/resize_and_webp_source.png)

## The problem

Here it is: **All three of those programs aren't available in the default environment Automator runs scripts in**.

The workflow depends on:
  * `image2webp`, which depends on:
    * `cwebp` (installed via homebrew)
    * `imagemagick` (installed via homebrew)

`image2webp` lives in my `~/bin` folder, along with a bunch of other home-grown scripts that I have in my `$PATH`. The other two are in `/usr/local/bin` as per usual.

The workflow can invoke `~/bin/image2webp` directly, but when that runs, it won't by default have the homebrew paths for `/usr/local/bin/{cwebp,imagemagick}`.

So how do we address that?

## Solution 1

Make the workflow source my `~/.zshrc`, which sets `PATH` to include `~/bin` and `/usr/local/bin`, and does a bunch of other (unneeded) stuff.

**Performance?**  `time zsh -c 'exit'` reports 0.00s, so performance seems okay.

**Security?** I control this workflow and my dotfiles (so no issue with my side of the code) and if there's any issue with homebrew, cwebp, imagemagick or a dependency, I have to accept that risk.

Seems simple, clean, and while it's theoretically inefficient, the evidence seems to say it's efficient enough.

**If I were at work, I'd stop here**. But this is a hobby, so let's see if it can be improved!

## Solution 2

Make the workflow hard-code the path to the script, and the script probably has to hard-code the paths to the binaries. Seems non-portable and fiddly.

## Solution 3

Make the workflow embed the whole script (instead of calling out to it), including the paths to the binaries.

Slightly more portable, much less fiddly, but means you can't invoke the script outside of calling the workflow (i.e. not from a regular terminal). And I do do that sometimes.

## Solution 4

Isolate the path settings: create a custom `~/.zsh_paths` or similar, source it from the workflow, and have that sourced by `~/.zshrc`.

This is basically what my `~/.profile` is - a set of environment variables.

## Solution 5

Make the workflow source `~/.profile` directly.

Yeah, this works nicely. It's cleaner than sourcing the whole `~/.zshrc` but doesn't constrain anything into being aware of custom paths.
This is also a good reminder to check whether I'm setting any paths in `~/.zshrc` directly. No, I'm not, and not in `~/.bashrc` either.

However, while reading [StackOverflow's most popular zsh question](https://superuser.com/q/187639/352136) [^1], I realised that my setup was slightly wrong. In short, mine was set up like this:

```zsh
# ~/.zshrc loads ~/.profile, if it exists, with this:
[[ -e ~/.profile ]] && emulate sh -c 'source ~/.profile'
# ~/.zprofile doesn't exist
```

But the consensus is that it should be like this:

```zsh
# ~/.zshrc has no awareness of profiles
# ~/.zprofile loads ~/.profile, if it exists, with the same command:
[[ -e ~/.profile ]] && emulate sh -c 'source ~/.profile'
```

So, that leads us to...

## Solution 6

I made the change above. Then the workflow won't need to explicitly load anything. It runs the built-in `/bin/zsh`, which will surely act like a sensible zsh and read the profile file itself.

When I tried this, it didn't work! The zsh provided by macOS doesn't read `~/.zprofile`. Why??

## Solution 7

Make the workflow load `~/.zprofile` (aka go right back to solution 5).

It still requires an explicit load line, but now it feels like the _right_ load line.

But wait, [what's this?](https://apple.stackexchange.com/a/388623/87067) There's a place explicitly for environment variables, called `.zshenv`?

## Solution 8

Move all profile lines into `~/.zshenv` and remove the explicit `~/.profile` read from `~/.zprofile`. This is read even in a non-interactive shell (which Automator workflows seem to be), so the source line can be removed from workflows.

**Upsides**: login and interactive shells both get the right paths set, all the time, with no manual sourcing.

**Downsides**: divorces zsh and other (i.e. bash) environments further. If I still use bash for anything other than from zsh (when those variables will be exported), the two configs will get out of sync.

## Solution 9

Delete all the bash config too and put a ring on zsh 💍. I'm initially wary, but then think - given that macOS has shifted off bash and onto zsh altogether, and remoting into bash-oriented hosts won't be affected, maybe I'm willing to take that chance for the sake of simplicity.

I'm probably going to start using zsh-specific features and then wonder in future, on other hosts, where that stuff is - but I'm already comfortable having a local blinged-up Vim and using remote stock Vims, so I'm OK with that.

Also, my bash config is [saved in git](https://github.com/d-lord/dotfiles/blob/4200600b65846a2c1912d9ab5e36035e192ad94e/.bashrc) so deletion is never permanent.

![Meme: "Friendship ended with bash. Zsh is my new best friend"](/assets/images/friendship_ended_with_bash_zsh.png)

If you've made it this far, congratulations. I hope this has helped you on whatever quest you were on. **Why not say hi in the comments?**

[^1]: [Source for that claim](https://superuser.com/questions/tagged/zsh?sort=MostVotes)
