---
layout: post
title:	"More asyncio queues"
excerpt: "Now with a signal object and termination/cleanup."
date:	2018-07-28 00:00:00
published: true
categories:
thumbnail: assets/thumb-more-asyncio-queues.png
tags: python performance asyncio programming
---

**This post is based on [a previous blog post]({{ site.baseurl }}{% post_url 2018-06-12-asyncio-queues %}).**

Hello, and welcome back to asyncio experiments in Python. Today, I'm extending a previous script. The 'push/pull' script used an asyncio Queue with one **push** coroutine and two **pull** coroutines to gradually fill up the queue. Push would gradually outweigh pull's ability to clear out the queue and they couldn't keep up.

OK, so what's changed? This version puts a finite number of items into the queue then waits for it to empty.

<center>
	<script type="text/javascript" src="https://asciinema.org/a/193852.js" id="asciicast-193852" async></script>
</center>

What does this mean architecturally?

- A signal object is instantiated to serve as a marker that the queue is "closed for business".
- The main function has to gather all three tasks (one push, two pull). Previously, the program would infinite-loop, so it didn't matter if I only waited on the infinite push.

It's a pretty minor change in code, but I had to figure out how to gracefully stop the pullers, and I think it's a much cleaner demo for it.

Here's the code.

```python
import asyncio
import random


async def pusher(queue: asyncio.Queue):
    """ Pushes an item to the queue every second. """
    counter = 0
    while counter < 20:
        await queue.put(counter)
        counter += 1
        await asyncio.sleep(0.5)
    await queue.put(DONE_SIGNAL)


async def puller(queue: asyncio.Queue, name: str):
    """ Fetches an item from the queue and takes a random timespan to "process" it. """
    while True:
        item = await queue.get()
        print(f"[{name:^6}] Got item: {item} (size {queue.qsize()})")
        if item is DONE_SIGNAL:
            queue.put_nowait(DONE_SIGNAL)
            print(f"[{name:^6}] Done")
            return
        await asyncio.sleep(random.random()*3)
        queue.task_done()


async def main():
    queue = asyncio.Queue()
    tasks = [asyncio.create_task(t) for t in (pusher(queue),
                                              puller(queue, name='Fred'),
                                              puller(queue, name='George'))]
    await asyncio.gather(*tasks)
    print(f"Queue size is now {queue.qsize()}")


class DoneSignal:
    def __repr__(self):
        return 'DONE_SIGNAL'  # make it debug-print nicely


DONE_SIGNAL = DoneSignal()


if __name__ == '__main__':
    main_loop = asyncio.get_event_loop()
    main_loop.run_until_complete(main())
```

As of Python 3.7, running Python with the `-X dev` option enables asyncio's debug mode, which is valuable for spotting tasks you've forgotten to clean up.
