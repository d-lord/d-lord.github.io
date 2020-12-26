---
layout: post
title:	"How to create a package in Spacemacs, for the programmer in a hurry"
excerpt: "Suppose you want to create a new minor mode called `bulletins-mode`, and you're feeling inspired, so you want to get it usable ASAP, without worrying about the ideal way to do it. This is slightly harder to do in Spacemacs than in core Emacs."
date:	2019-04-25 00:00:00
published: true
thumbnail: assets/images/emacs-sample-bulletins-mode.png
tags: emacs spacemacs
---

This is a quick guide to something I couldn't find an answer for online. [Spacemacs](http://spacemacs.org/) is a great distribution, but it can complicate things sometimes with its notion of layers and packages.

Suppose you want to create a new minor mode called `bulletins-mode`, and you're feeling inspired, so you want to get it usable ASAP, without worrying about the ideal way to do it. This is slightly harder to do in Spacemacs than in core Emacs.

Here's how to rush through the process.

# Make your own local package in Spacemacs

| path | comment |
|---|---|
| `~/.emacs.d/private/local` | You should already have this folder and it'll contain `README.md`. |
| `~/.emacs.d/private/local/bulletins-mode` | Create a new folder for your package. |
| `~/.emacs.d/private/local/bulletins-mode/bulletins-mode.el` | Create a file for your code, with example contents [below](#example-bulletins-modeel-code). |
| `~/.spacemacs` | Scroll down to `dotspacemacs-additional-packages` and add `('bulletins-mode :location local)`. |
| `~/.spacemacs` | Also scroll down to `dotspacemacs/user-config` and in the body, add `(require 'bulletins-mode)`. |

## Example bulletins-mode.el code
```elisp
;;;###autoload
(defun bulletins-mode/sample-function ()
  "If you can see this with M-x, you've set up your local package properly."
  (interactive)
  (message "bulletins-mode is present!"))

(provide 'bulletins-mode)
```

## Validation

Type `SPC f e R` to reload your `~/.spacemacs` configuration.

Try typing `M-x bulletins-mode/sample-function RET`. If you see your function and it runs correctly, then congratulations, you're sorted!

If you don't, type `SPC b b`, visit the `*Messages*` buffer, and look for error messages containing `bulletins-mode`.

# What just happened?

The `private/` directory isn't tracked by Spacemacs' Git system. Inside it, `local/` is for storing local packages. So we created a `bulletins-mode/` folder for the package containing `bulletins-mode.el` for the code.

We then told `~/.spacemacs` to look for this package locally, and then told it to load the mode after initialization.

(Most of this is documented in the README.md files along the way, but the `:location local` bit is harder to find).

# What next?

OK, so this was a maximum-haste-minimum-complexity prototype. If it goes well, how should you do it "properly"?

- Write an actual package in that folder which does something useful :)
- You can split your package into multiple files if you want.
- You can create a custom layer with `M-x configuration-layer/create-layer RET`.
- If the prototype is successful, you should probably make it into a proper layer. Read the Spacemacs docs (below) to figure it out yourself. I'm not there yet. :)
  - They [aren't keen on `require`](http://develop.spacemacs.org/doc/LAYERS.html#no-require), so that's definitely an improvement to make.
- You can also compile packages with `M-x byte-compile-file` to speed up loading.

# Wrapping up

This was written with Spacemacs v0.200.13, on Emacs 26.2, on macOS.

Big reading: [Spacemacs: Configuration layers](http://develop.spacemacs.org/doc/LAYERS.html).

Source for much of this post: [Spacemacs doc: 6.3.2: Configure packages without a layer](http://develop.spacemacs.org/doc/DOCUMENTATION.html#without-a-layer). This is a good start, but still tries to fetch from MELPA rather than loading locally.
