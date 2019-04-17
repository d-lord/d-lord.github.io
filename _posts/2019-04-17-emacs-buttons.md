---
layout: post
title:	"Making Emacs buttons with CVE IDs"
excerpt: "Adventures in customising Emacs."
date:	2019-04-17 00:00:00
published: true
categories:
thumbnail: /assets/emacs_cve_after.png
tags: emacs
---

A CVE is an identifier for a specific security vulnerability in a specific product. You might have heard of such CVEs as [CVE-2014-0160](https://heartbleed.com/), [CVE-2017-5754](https://meltdownattack.com/) and [CVE-2017-13077 through 13088](https://www.krackattacks.com/), also known as Heartbleed, Meltdown and KRACK.

Technically the full name is a [Common Vulnerabilities & Exposures Identifier](https://cve.mitre.org/), but that's a mouthful.

At work we deal with a _ton_ of these CVEs on a daily basis. There are a few online databases where you can go to look up details. Everyone has their own pet script for making this lookup fluent.

Here's my latest, for Emacs on macOS:

```elisp
(defun buttonize-buffer-with-cves (bufname)
  "Mark CVEs in a given buffer as hyperlinks."
  (interactive "bBuffer to add CVE buttons to: ")
  (save-excursion
    (with-current-buffer bufname
      (goto-char (point-min))
      (while (re-search-forward "CVE-[[:digit:]]\\{4\\}-[[:digit:]]\\{4,\\}" nil t)
        (let* ((start (match-beginning 0))
               (end (match-end 0))
               (cve (buffer-substring start end))
               (lexical-binding t))
          (make-button (match-beginning 0) (match-end 0)
                       'url (format "https://nvd.nist.gov/vuln/detail/%s" cve)
                       'action (lambda (button)
                                 (let ((url (button-get button 'url)))
                                   (shell-command (format "open '%s'" url)))
                                 'help-echo (format "Visit %s at NVD" cve))))))))
```

With this, you can go from:

![Screenshot of before.](/assets/emacs_cve_before.png)

To:

![Screenshot of after.](/assets/emacs_cve_after.png)

Now all the CVEs in the document are highlighted and clickable! Note that I've moused over CVE-2018-18498 and can middle-click it (or select it with the keyboard and press Enter) to open [its corresponding CVE page](https://nvd.nist.gov/vuln/detail/CVE-2018-18498). Yay!

## Interesting bits of the code
```elisp
(interactive "bBuffer to add CVE buttons to: ")
```
[interactive is a cool macro](https://www.gnu.org/software/emacs/manual/html_node/elisp/Using-Interactive.html) and this lets you invoke the function interactively, with buffer names automatically suggested/completed by Emacs.

```elisp
(while (re-search forward ...))
```
Nice, easy way to iterate over matching text. You can use [match-beginning and match-end](https://www.gnu.org/software/emacs/manual/html_node/elisp/Simple-Match-Data.html) to get the range of the match.

```elisp
(re-search forward "CVE-[[:digit:]]\\{4\\}-[[:digit:]]\\{4,\\}" nil t)
```

All the Emacs Lisp docs cite `\{` and `\}` for repetition, but because of escaping, what you actually use is `\\{`. Fun times. Thanks to [`M-x regexp-builder`](https://www.masteringemacs.org/article/re-builder-interactive-regexp-builder) for making this (relatively) quick to diagnose, once I'd given up reading the docs and started experimenting.

```elisp
(make-button ... 
             'url (format ...))
             'action (lambda (button)
                             (let ((url (button-get button 'url)) ...) ...))
```

You can set arbitrary properties like `'url` on the button and retrieve them later with `(button-get button 'url)`. Also, I _think_ this is safe, but no guarantees. The argument to `open` is single-quoted so you'd need a single quote to break out of it, but the pattern being matched doesn't permit single quotes. Not gonna lie, getting this working was enough trouble without going into shell escaping.

## How do you make this useful?

Short term: You can `M-x buttonize-buffer-with-cves`.

Long term: It could be built into a mode you're already creating...

## No, but how do you make it useful enough to be worth the time investment?

Goodbye!
