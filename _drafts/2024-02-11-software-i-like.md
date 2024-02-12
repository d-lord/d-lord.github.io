---
layout: post
title:	"Software I like"
date:	2024-02-11 00:00:00
published: true
excerpt: '.'
tags: macos software programming
nofancybox: true
thumbnail: assets/images/software_i_like_hero.png
---

Sometimes I pay for software. Weird, right? There's a lot of it out there, and it's famously hard to convince people to hand over money for it. 

This is a list of all the stuff I've recommended more than once, usually until someone has given me a glassy-eyed stare and agreed to try it. It's mostly free-as-in-beer, with some paid licences adding more features, or a way to donate.

![Colourful header with logos of these programs](/assets/images/software_i_like_hero.png){:.center-image}

# On macOS

I like Apple's desktop OS, and the software below makes it even better than the default experience.

---

## Better window management

If you ask me, Microsoft got window management right. I'm comfortable with how Mac does it, but these two bring over some of the Windows sparkle, and you're better off for it.

### [Rectangle](https://rectangleapp.com/)

Makes it easy to move windows around, snap them to an edge, resize them, and move them across monitors. Think of winkey + arrows. Provides key bindings and drag-drop. The shortcuts take some time to learn - I made an [Anki](https://apps.ankiweb.net/) deck for this - but now it’s great. I really missed this for the first few years on macOS.

### [AltTab](https://alt-tab-macos.netlify.app/)

Does what it says on the box, and quite pretty too. At heart, I use it because it's better than Apple's `` ⌘+` `` and `⌘+↹`, but it's also very configurable in terms of keys and appearance, and has some extra functionality.

---

## Utility

### [MonitorControl](https://github.com/MonitorControl/MonitorControl)

This does one thing exceptionally well: I use twin Dell monitors connected with USB-C, and this lets you change their brightness just like your laptop screen. [^3] When writing this blog post I realised I’d never contributed, so chucked them $10.

### [BetterTouchTool](https://folivora.ai/)

Apple's trackpads are great, but this elevates them to "better than a mouse" for me. For work, anyway. I've added some gestures for browsers and file managers: 2-finger swipe up to open a new tab, 2-finger swipe down to close it, and tiptap left/right to change tab. It's far more powerful than I use it for, and that also makes it fun to play with on long trips without internet service.[^1]

### [Alfred](https://www.alfredapp.com/)

Great fuzzy finder with a ton of built-in features, plus it's very programmable, which makes it fun to extend. The native Spotlight has supposedly improved since I started using Alfred, but by now, it's just too powerful (and customised) to leave. I use it daily for instant lookups like [country codes](https://github.com/d-lord/alfred-country-codes), [HTTP status codes](https://github.com/d-lord/alfred-http) and [IATA codes](https://github.com/d-lord/alfred-iata-codes). I also saved a bunch of snippets so it's easy to type fancy characters like `⌘` with `snip cmd`. Fallback searches are great too; being able to hit `⌘+space` and type `wiki foo` to go straight to Wikipedia's page on Foobar is just lovely.

### [1Password](https://1password.com/)

Everyone should be using a password manager. Given how many passwords your digital life requires, and how common [credential stuffing](https://www.abc.net.au/news/2024-01-19/what-is-credential-stuffing-scams-how-to-prevent-and-protect/103367570) and data breaches are, it's essential hygiene. I'm not telling you to use 1P necessarily, but it's been pretty good to me.

Their Families plan is good value for the geek in the family, and I was paying for it for ages. Sometimes, if your employer uses it, the enterprise plans include a free Families licence for you. 😉

### [Camo](https://reincubate.com/camo/)

Lets you use your phone as a webcam. For a lot of people, your phone is the best camera you have, and far better than whatever your laptop has.

Apple's [Continuity Camera](https://support.apple.com/en-au/guide/mac-help/mchl77879b8a/mac) has largely eaten Camo's lunch, but I still use it for some extra dials and knobs.

---

## Programmer stuff

### [iTerm](https://iterm2.com/)

The best terminal I've used, and I sponsor it on GitHub. Generally really nice with sensible defaults, good support, and [powerful features](https://iterm2.com/features.html). Heaps of them are useful, like split panes, command-clicking URLs, smart selection, infinite scrollback and shell integration. Heaps are just cool for tinkering, like coprocesses, triggers and showing images. Bonus points for its tmux integration which I don’t use regularly but is amazing when I do - it uses native tabs and panes for tmux's own.

### [Dash](https://kapeli.com/dash)

Amazing software, and it probably helped me get hired at Canva. Quick access to all sorts of documentation[^2], and in a native app. I adore it for Python. The default docsets, and those submitted by the community, are super broad. Not a fan of its new subscription model though, so I'll probably be using my Dash 6 licence indefinitely.

---


# Web browsing and social media

### [Firefox](https://getfirefox.com)

The last browser before Chromium eats everything. Also has [customisations I value](/2019/12/07/tinfoil-firefox/), like separate search and address bars.

### [uBlock Origin](https://ublockorigin.com/)

Just a good ad blocker. I think ad blockers in general are crucial to a good internet experience. This also has a good picker interface to let you block anything you like, such as Twitter's "trending" sidebar.

### [FB Purity](https://www.fbpurity.com/)

An absolute marvel for Facebook users. You know those chain letters you see about "like and share this post to see your friends' activity again"? This actually works, and lets you block a lot of the trash injected into the feed and sidebar as well. It surely won't be around forever, but for now, it does an outsized amount to make the internet a better place for real people.

### [DeArrow](https://dearrow.ajay.app/)

Brings your YouTube experience up a few IQ points, by reducing clickbait in thumbnails and titles.

You know how you turn off your adblocker, and suddenly the web is a garish carnival of adverts willing to use any trick to grab your attention? Viewing YouTube on a client without DeArrow is starting to feel a bit like that.

Makes it super easy to contribute your own improvements, too. I'm a sucker for editorial control.[^4]

---

[^1]: Everyone finds fun in their own way. I know a guy who went on a spree in his home workshop and put everything on wheels.
[^2]: It's not quite "lookups [at the speed of thought](https://duckduckgo.com/?q=vim+edit+at+the+speed+of+thought)", but pretty close.
[^3]: The physical buttons tucked away behind the monitor are annoying to use, and MonitorControl has the same effect. It doesn't have to fiddle with gamma to fake it.
[^4]: The before/after comparison on their homepage is pretty cool too.
