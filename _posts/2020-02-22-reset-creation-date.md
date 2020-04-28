---
title: "Forcing iCloud to keep a local copy of a file"
date:	2020-02-22 00:00:00
excerpt: "iCloud is not as smart as it should be and can't be configured enough to make up for that, so here's a workaround."
published: true
---

**Update: It seems that this isn't a reliable solution**. [It can be done in iCloud for Windows, but not for macOS](https://support.apple.com/en-au/guide/icloud/mm17d7e476fc/icloud).

<hr>

iCloud on macOS has a helpful button labelled "Optimise Mac Storage". It's good if you have limited space on your Mac - it'll copy your Desktop and Documents folders in the cloud and only keep the most recent files on your computer.

It's also not the brightest. I have an [Alfred preferences file](https://alfredapp.com) which was created in 2013, and I keep it in iCloud to sync across multiple Macs. iCloud keeps deleting the local copy, presumably because it's an old file. You can force it to download it again by clicking this cloud icon, but it doesn't learn, and next time it sweeps, it'll delete it again:

<figure>
<img src="/assets/images/alfred-settings-desynced.png" alt="Screenshot of Finder showing a cloud icon next to the Alfred preferences file to indicate that it's on the cloud" />
<figcaption>The cloud button means "I want it", but apparently not "I'll still want it in an hour".</figcaption>
</figure>

So when you try to use Alfred, it shows this error:
<img src="/assets/images/alfred-error-no-preferences.png" alt="Screenshot: Alfred showing an error message: 'You have a sync location set, but the preferences package is missing'" />

You might think "oh, I'll just configure iCloud to preserve that folder" but, unlike every other cloud sync product in 2020, it can't do that.

Turns out, you can get around this by modifying the creation time of the file or folder on the filesystem. Also turns out, the `touch` utility can only do modification and access times, so you need something else.

## Install the Xcode command-line tools

You'll need one of the tools in this kit. If you haven't installed it before:

```sh
$ xcode-select --install
```

Follow the prompts.

## Use `setfile` to modify creation time

```sh
$ setfile -d "02/20/2020 00:00:00" Alfred.alfredpreferences
```

Note that the datestamp is in the "backwards" month-first format.

If you're reading this in the distant future, adjust the timestamp to be recent.
