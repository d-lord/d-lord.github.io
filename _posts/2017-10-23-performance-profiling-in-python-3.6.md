---
layout: post
title: "High-performance network requests in Python 3.6"
date: 2017-10-23 00:00
published: true
excerpt: "aiohttp is pretty good."
tags: python async aiohttp
thumbnail: /assets/2017-10-23-python-profiling-results.png
---

I'm in two social Slack teams at once. [UQCS](https://uqcs.slack.com) has a thousand members and a thousand custom emoji. The other has a few tens of members and no custom emoji.

I wanted to duplicate some of the finer emoji to add 'core functionality' like `:this:`, the `:thonking:` family, the `:partyparrot:` family and a few language/product icons.

[Slack-Emojinator](https://github.com/smashwilson/slack-emojinator) exists, and it can both download and upload sets of emoji. I wanted to do some of my own HTML parsing to get experience with BeautifulSoup, so I wrote my own version of the first half.

Now, [Slackmoji-Swarm](https://github.com/d-lord/slackmoji-swarm)[^1] is my faster method to bulk-export emoji, while remaining compatible with Emojinator's bulk upload.

[^1]: Names are hard.

Scalability is important, because 1,000 emoji is actually quite a lot - the naive solution took over 5 minutes to download on my home connection.

Long story short, I wrote three versions: one naive, one reusing connections in `requests`, and one full rewrite with `aiohttp` ("asyncio http"). The results speak for themselves.

![Progressive improvement in runtime](/assets/2017-10-23-python-profiling-results.png)

Now that I've written it, tested it, profiled it, and used it, it might be worth submitting as a pull request to the Emojinator project.

### Bibliography

Thanks to:

[aiohttp's documentation](http://aiohttp.readthedocs.io/en/stable/)

["Illia"'s writeup of high-performance HTTP in Python](https://iliauk.com/2016/03/07/high-performance-python-sessions-async-multi-tasking/)

Check out [the project on GitHub](https://github.com/d-lord/slackmoji-swarm/)!
