---
layout: post
title: "Debugging Unicode in Python 3.4"
date: 2017-10-08 00:00
published: true
excerpt: "This started as a series of notes entitled \"The Tinkerer's Guide to Unicode\", but I got stuck into analysing Windows' behaviour via Python."
tags: python text unicode
---
_This started as a series of notes entitled "The Tinkerer's Guide to Unicode", but I got stuck into analysing Windows' behaviour via Python. The steps involved may be useful to others, so I've written up the process._

- ASCII: old-school character set favouring American-English characters. Uses 7 bits, so 128 entries from 0-127. [Full table](https://www.ascii-code.com/)
- ANSI: newer character set and encoding, superset of ASCII using the eighth bit (so values 128-255). Different *code pages* determine what's in the eighth bit's range.
- Unicode: the modern character set, now completely distinct from an encoding. Values look like `U+0023` (`#`).
- UTF-8: encoding system for Unicode. Code points are one or more bytes. Named for 8 bits.
- UTF-16: encoding system for Unicode. Code points are two or more bytes. Named for 16 bits.
- UCS-2: a predecessor to UTF-16. Named for 2 bytes. [They're slightly different](https://www.ibm.com/support/knowledgecenter/en/ssw_ibm_i_71/nls/rbagsucs2.htm). 😩

## Inserting characters

We'll use `Đ` as a sample character. It has Unicode value `U+0110`.

### charmap for visual exploration
Run `charmap`. Tick "advanced view" down the bottom. Ensure "Character set:" is "Unicode". _Don't_ use "Search for:" at the bottom; instead "Go to Unicode:" on the right. Entering "0110" will get you your U+0110 'Đ'. Press "Select" then "Copy", and paste into your application.
![charmap for Đ.png]()

Copy+paste the character around everywhere.

### Direct input with `Alt`+`+0110`
I seem to have decimal input enabled by default, _but_, it's not Unicode input without a registry hack. Holding `Alt` then pressing the numpad `+` produces a bell. Holding `Alt` then typing `0110` on the numpad produces `n`, [which is decimal 110 in ASCII](https://www.sciencebuddies.org/science-fair-projects/references/table-of-8-bit-ascii-character-codes).

[The registry hack](https://superuser.com/q/1204586/352136) requires a reboot, but changes the behaviour above to enable `Alt`+`+` and now I'm alting unicode.

We can also insert non-numpad digits, ie A through F, as seen in `U+00CF` `Ï`.

But wait, there's more! `U+1F629`, the 'weary' emoji `😩`, is _five_ hex digits. There doesn't seem to be a built-in way to enter this.

Let's just... use [Emojipedia](https://emojipedia.org/weary-face/) for this one, to copy+paste it...

*Remember to press `+` when inputting Unicode.*


## Debugging characters

Suppose you go to insert `U+1F629`, our weary friend, and get a box: ''. It's not '😩' because you have one of those, rendered correctly, in the same session. So what is it? How does it relate to `U+1F629`?

### Windows 10

A quick Google doesn't reveal any utilities.

Fortunately, I've installed Bash on Windows and it has Python 3.4.3. I know the `ord()` function will tell you the Unicode code point for a single-character string, so let's try `ord("")`.

It turns out, in this terminal, you can't enter characters with `Alt`, even our faithful `#`/`U+0023`. You can copy+paste "regular" text (for some value of "regular") but not '😩' or the box we're trying to debug. If you paste a sentence including those characters, they simply won't register.

```python
Python 3.4.3 (default, Nov 17 2016, 01:08:31)
[GCC 4.8.4] on linux
>>> len("") # Attempting to paste something between the quote marks...
0
>>> ord("") # Pressing Alt + +U0023 also does nothing.
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: ord() expected a character, but string of length 0 found
```

So the Bash for Windows terminal is dumb. Whatever.

It turns out that both `cmd.exe` and `powershell.exe` behave the same in these next steps, so I'll stick with PowerShell:

```python
Python 3.6.2 (v3.6.2:5fd33b5, Jul  8 2017, 04:57:36) [MSC v.1900 64 bit (AMD64)] on win32
>>> ord("😩") # "weary". Pasting it in will work, but it'll display as two boxes.
128553
>>> ord("") # the box we're trying to debug - supposedly U+1F629 but isn't. Displays as one box.
63017
```

Now we're getting somewhere. The terminal only has limited support for our fancy Unicode characters, but we can operate on them.

It seems that rendering a box '□' is Windows' standard response to "unknown", not eg the replacement character '�' which often looks like '<?>'. [^1]

[^1]: Incidentally, I tried the Lucida Console, Consolas, DejaVu Sans Mono, and Source Code Pro fonts. They all showed the same boxes.

So, why is one 128553 and the other 63017?

Let's use the [unicodedata](https://docs.python.org/3/library/unicodedata.html) library.

```python
>>> weary = "😩" # "□□": displays as two boxes
>>> bugbox = "" # "□": displays as one box
>>> ord(weary), ord(bugbox)
(128553, 63017)
>>> from unicodedata import name
>>> name(weary)
'WEARY FACE'
>>> name(bugbox) # This is not an error - the code point has no name.
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: no such name
>>> weary
'😩' # '□' this displays as a box in the terminal!
>>> bugbox
'\uf629' # this is interesting.
>>> len(bugbox)
1 # OK, so it's definitely one code point - it's not a string with two, eg 'f\u629'
>>> e_acute = "é" # "e□": this displays with a box, because...
>>> len(e_acute)
2 # because it's actually two code points! 
>>> print([unicodedata.name(c) for c in e_acute])
['LATIN SMALL LETTER E', 'COMBINING ACUTE ACCENT'] # the second is a combining diacritic, in fact.
>>> print([ord(c) for c in e_acute])
[101, 769]
```

Sidetrack: what's the actual code point for "COMBINING ACUTE ACCENT"? I found [a PDF of combining diacritics](http://www.unicode.org/charts/PDF/U0300.pdf) which tells us the answer is `U+301`, but how do you learn that from Python?

```python
>>> unicodedata.combining(e_acute[1])
230 # Cool. The method would return 0 if it weren't a combining code point. But what is 230?
>>> unicodedata.name(chr(230))
'LATIN SMALL LETTER AE' # Hmm... not likely...
>>> chr(230)
'æ' # Nope. Wrong track. Let's ignore the 'combining' value for now.
>>> chr(769) # Returning to ord(c) to check it goes both ways.
'́' # Another box. ... Well, you'd expect it to look like a COMBINING ACUTE ACCENT, so like an apostrophe?
>>> 'a' + chr(769)
'á' # Shows as 'a□', but pasting into Notepad++ indicates it's an a-acute character (just formed from two code points). Good.
>>> e_acute[1] == chr(769)
True # Just for safety's sake. We've got this under control.
```

Incidentally, copy+paste is doing _much better_ than the terminal. Notepad++ is taking "boxes" from the terminal and displaying them correctly.

Anyway, how do we go from '769' (from `ord()`) to our `U+0301` code point?

```python
>>> 0x301
769
>>> hex(769)
'0x301'
```

Oh. Right. I'd forgotten Unicode code points are specified in hex.

*Sidetrack complete*. Back to our mysterious `\uf629` character, aka `bugbox`.

```python
>>> bugbox
'\uf629'
>>> 0xf629
63017
>>> 0x1f629
128553 # This seems... wrong.
>>> weary
'😩' # '□□', recall.
>>> [c for c in weary]
['😩'] # ['□□']
>>> [c for c in "foo{}".format(weary)]
['f', 'o', 'o', '😩'] # ['f', 'o', 'o', '□□'] - okay, definitely one character, just making sure.
>>> weary, bugbox
('😩', '\uf629')
>>> ord(weary), ord(bugbox)
(128553, 63017)
>>> hex(ord(weary)), hex(ord(bugbox))
('0x1f629', '0xf629') # !!!!
```

*This is the key*. The first hex digit is dropped when entering the five-digit character into PowerShell. What about other characters?

I went to write a loop test, but ran into something else. You can't simply specify 'weary' as `\u1f629`.

```python
>>> '\u1f629'
'ὢ9' # this isn't 'weary'?
>>> chr(0x1f629)
'😩' # not the same!
```

Turns out, [in Python 3 strings](https://docs.python.org/3.3/howto/unicode.html#the-string-type), `\u` is for 16-bit hex values and `\U` is for 32-bit hex values.

```python
>>> ord('\U0001f629')
128553
>>> ord(weary)
128553
>>> hex(128553)
0x1f629
```

Great. I'm satisfied that this all fits. Let's write some looped tests.

```python
>>> import collections
>>> Emoji = collections.namedtuple("Emoji", ("code", "character"))
>>> data = [Emoji(*e) for e in [('\U0001f629', '😩'), ('\U0001f525', '🔥'), ('\U0001f451', '👑'), ('\U0001f3f0', '🏰')]]
>>> print([e.character == e.code for e in data])
[True, True, True, True]
```

_Pictured: the best, dumbest Python I've ever written._

Now I'll populate the `Emoji.character` by typing the escape codes on my keyboard. This is it: the main test.

```python
>>> manually_input_data = [Emoji(*e) for e in [
...     ('\U0001f629', ''),
...     ('\U0001f525', ''),
...     ('\U0001f451', ''),
...     ('\U0001f3f0', '')]]
>>> manually_input_data
[Emoji(code='😩', character='\uf629'), Emoji(code='🔥', character='\uf525'), Emoji(code='👑', character='\uf451'), Emoji
(code='🏰', character='\uf3f0')]
>>> print([e.character == e.code for e in manually_input_data])
[False, False, False, False]
```

Oh no! They're all wrong. Let's take a closer look.

```python
>>> print(["Should be: {}, is: {}".format(ord(e.code), ord(e.character)) for e in manually_input_data])
['Should be: 128553, is: 63017', 'Should be: 128293, is: 62757', 'Should be: 128081, is: 62545', 'Should be: 127984, is: 62448']
>>> print(["Should be: {}, is: {}".format(hex(ord(e.code)), hex(ord(e.character))) for e in manually_input_data])
['Should be: 0x1f629, is: 0xf629', 'Should be: 0x1f525, is: 0xf525', 'Should be: 0x1f451, is: 0xf451', 'Should be: 0x1f3f0, is: 0xf3f0']
```

This is showing a familiar pattern. All the entered code points are missing the leading `1`.

This is a very limited test data set, of course. The main emojis block is only [approximately `U+1F300` to `U+1F9C0`](https://en.wikipedia.org/wiki/Emoji). Let's broaden it, and update the model while we're at it.

```python
>>> Character = collections.namedtuple("Character", ("correct_codepoint", "entered"))
>>> data = [
...     ('\u0110', 'Đ'),
...     ('\u0301', '́'),
...     ('\u2048', '⁈'),
...     ('\uff9a', 'ﾚ'),
...     ('\U00010380', '΀'),
...     ('\U00010391', 'Α'), # entered as alt+ "+10391"
...     ('\U00010391', 'Α'), # entered as alt+ "+00010391"
...     ('\U00020013', '^S'), # entered as alt+ "+20013"
...     ('\U00020013', '^S')] # entered as alt+ "+00020013"
>>>
>>> characters = [Character(*e) for e in data]
>>> print([e.correct_codepoint == e.entered for e in characters])
[True, True, True, True, False, False, False, False, False]
>>> # Hmm. This looks familiar.
>>> print([e.correct_codepoint <= '\uffff' for e in characters])
[True, True, True, True, False, False, False, False, False]
```

As soon as we start entering 32-bit values, the terminal starts trimming them.

```python
>>> for c in characters:
...     print("{:>8}: {}".format(hex(ord(c.correct_codepoint)), hex(ord(c.entered))))
...
   0x110: 0x110
   0x301: 0x301
  0x2048: 0x2048
  0xff9a: 0xff9a
 0x10380: 0x380
 0x10391: 0x391
 0x10391: 0x391
 0x20013: 0x13
 0x20013: 0x13
```

Let's look at the subtraction between correct and entered:

```python
>>> for c in characters:
...     print(hex(ord(c.correct_codepoint) - ord(c.entered)))
...
0x0
0x0
0x0
0x0
0x10000
0x10000
0x10000
0x20000
0x20000
```

All nicely formatted:

```python
>>> for c in characters:
...     print("{:35}: {:>8} {:<8} || {}".format(unicodedata.name(c.correct_codepoint), hex(ord(c.correct_codepoint)), hex(ord(c.entered)), hex(ord(c.correct_codepoint) - ord(c.entered))))
...
LATIN CAPITAL LETTER D WITH STROKE :    0x110 0x110    || 0x0
COMBINING ACUTE ACCENT             :    0x301 0x301    || 0x0
QUESTION EXCLAMATION MARK          :   0x2048 0x2048   || 0x0
HALFWIDTH KATAKANA LETTER RE       :   0xff9a 0xff9a   || 0x0
UGARITIC LETTER ALPA               :  0x10380 0x380    || 0x10000
UGARITIC LETTER ZU                 :  0x10391 0x391    || 0x10000
UGARITIC LETTER ZU                 :  0x10391 0x391    || 0x10000
CJK UNIFIED IDEOGRAPH-20013        :  0x20013 0x13     || 0x20000
CJK UNIFIED IDEOGRAPH-20013        :  0x20013 0x13     || 0x20000
```

So we've been dropping the first hex digits in the larger entries, and only attending to the last four.

Well, shit. That's probably why.


## Wrapping up

What a rollercoaster. Let's summarise:

- PowerShell and cmd have a limited range of characters visible in their available fonts (no emoji!).
- Bash for Windows' terminal will discard unfamiliar characters when pasted in. It also doesn't accept the `Alt`+`+0023` format even for familiar characters (eg `#`).
- PowerShell only preserves the last four hex digits from `Alt` input. So `Alt`+`+1F629` is trimmed to `U+F629`.

Useful techniques:

- Wrap a character in `hex(ord())` to view it in the same format as its Unicode identifier. Eg `hex(ord('😩')` will produce `'0x1f629'`
- Python 3's strings use `\u` for 4-digit unicode hex, but `\U` for 8-digit hex.
- Putting together test data and manipulating it with list comprehensions and print formatting is great for getting a picture of things.

## References

The spirit of experiment in this post was inspired by [Fluent Python](https://www.amazon.com/Fluent-Python-Concise-Effective-Programming-ebook/dp/B0131L3PW4), which I would strongly recommend to anyone seeking to become confident in the language. Chapter 4 deals with Unicode.
