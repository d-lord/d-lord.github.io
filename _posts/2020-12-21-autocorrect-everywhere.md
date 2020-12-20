---
layout: post
title: "How to turn off autocorrect everywhere"
excerpt: "For the control freak who thinks they make fewer mistakes than the computer does, and is probably right."
date:	2020-12-21 00:00:00
published: true
tags: macos pycharm slack office
---

Imagine for a moment that you know better than your computer, and your keystrokes flow directly from your intent. Or that natural-language models seem to have gone backwards in the decade since machine learning replaced the intelligently-written programs under the hood of many computer text interfaces, and you prefer not to allow the new model to intercede constantly. Or that you handle a lot of text with jargon, and you don't enjoy your computer interrupting you like a bored child to ask: "what's this word?" "that's not a real word" "nuh uh".

Well, you can live that dream.

When I replaced my faithful MacBook recently, I realised that I'd spent seven years gradually turning off autocomplete in almost every place it was enabled, and suddenly had to do it all in one go. Here are some notes on how to take back control.

## macOS

Open System Preferences. Go to `Keyboard` > `Text` and untick:

* Correct spelling automatically
* Capitalise words automatically
* Touch Bar typing suggestions (this is important as it protects you from iOS-style ~~distractions~~ suggestions)

"Use smart quotes and dashes" is quite nice and mostly harmless, though.

## PyCharm

This presumably applies to other JetBrains products like IntelliJ IDEA too.

Open Settings (`⌘+,`) > `Editor` > `Inspections` > `Proofreading` and untick the bold header.

![Screenshot of PyCharm's inspection settings with "Proofreading" highlighted.](/assets/images/PyCharm-proofreading.png)

Most notably, this will protect you from "typos" in your code or strings that are acronyms.

## Word

Word on macOS seems to keep this in two different places.

Open the Preferences pane (`⌘+,`), and then:

* `AutoCorrect` > `Automatically correct spelling and formatting as you type`.
* `Spelling & Grammar`, and untick: 
  * `Always suggest corrections`
  * `Check spelling as you type`
  * `Flag repeated words`
  * `Frequently confused words`
  * Look to the bottom of the pane, and untick:
  * `Check grammar as you type`
  * `Check grammar with spelling`

## Excel

Preferences (`⌘+,`) > `AutoCorrect` and untick:

* `Capitalise names of days`
* `Correct TWo INitial CApitals`
* `Capitalise first letter of sentences`
* `Replace text as you type`

While you're here, why not kick back and enjoy reading some pedantic group's list of auto replacements deployed to millions of computers worldwide, including `driveing`, the missing `é` in `eclair`, removing the apostrophe-e in `you're own`, and fixing `hasn;t` `don;t` and `you;d` (these ones I can kinda support).

## macOS Mail

Preferences (`⌘+,`) > `Composing` > `Check spelling`: Never.

It also has "When I click Send", which is a cool idea if you're into that.

![Screenshot of the settings page in macOS Mail.](/assets/images/macOS-mail-spellcheck.png)

## Slack

Open Preferences (`⌘+,`) > `Language & region` > `Enable spellcheck on your messages`.

![Screenshot of Slack preferences.](/assets/images/slack-autocorrect.png)
