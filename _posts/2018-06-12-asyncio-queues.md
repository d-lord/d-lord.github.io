---
layout: post
title:	"asyncio queues"
date:	2018-06-12 00:00:00
published: true
categories:
tags: python performance programming
---

Here's a toy program I wrote today in Python 3.6, to try out the [`asyncio.Queue`](https://docs.python.org/3.6/library/asyncio-queue.html) object. One coroutine pushes an item to the queue every second, and two more fetch them from the queue and "process" them. Over time, the processors are too slow and the queue builds up.

Of special note is the fact that the two processors never get the same item. That's pretty neat when you think about it.

And it's all a single thread! If these processors were based on I/O, you could run a lot more than two in parallel.

```python
import asyncio
import random


async def pusher(queue: asyncio.Queue):
    """ Pushes an item to the queue every second. """
    counter = 0
    while True:
        await queue.put(counter)
        counter += 1
        await asyncio.sleep(1)


async def puller(queue: asyncio.Queue, name: str):
    """ Fetches an item from the queue and takes a random timespan to "process" it. """
    while True:
        item = await queue.get()
        print(f"[{name:^6}] Got item: {item} (size {queue.qsize()})")
        await asyncio.sleep(random.random()*5)
        queue.task_done()


async def main():
    queue = asyncio.Queue()
    tasks = [asyncio.create_task(t) for t in (pusher(queue), puller(queue, name='Fred'), puller(queue, name='George'))]
    await tasks[0]


if __name__ == '__main__':
    main_loop = asyncio.get_event_loop()
    main_loop.run_until_complete(main())
```

Sample output:
```
[George] Got item: 99 (size 16)
[ Fred ] Got item: 100 (size 17)
[George] Got item: 101 (size 18)
[ Fred ] Got item: 102 (size 17)
[ Fred ] Got item: 103 (size 18)
[George] Got item: 104 (size 19)
[ Fred ] Got item: 105 (size 20)
[George] Got item: 106 (size 21)
```

As always, a huge thanks to [Dash](https://kapeli.com/dash) for making it easy to explore random libraries. And to a lesser extent, [Alfred](https://www.alfredapp.com) for providing lookups almost at the speed of thought.

<figure>
<img src="/assets/images/alfred-dash.png" alt="Screenshot of Alfred showing Dash search results" />
<figcaption>`⌥+space dash asyncio.queue` takes about a second to type if you know what you're after, and Alfred will populate the list smoothly.</figcaption>
</figure>

<figure>
<img src="/assets/images/dash-asyncio-queue.png" alt="Screenshot of the resulting search in Dash."/>
<figcaption> Pressing `enter` brings up this handy interface in Dash with a ton of information and good keyboard navigation (although I'm still learning it!). </figcaption>
</figure>
