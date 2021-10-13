---
title: "Prettypipe"
excerpt: "My very first Rust program helps you distinguish between stdout and stderr."
published: true
date: 2021-10-13 00:00:00
thumbnail: assets/images/thumb-prettypipe-intro.png
tags: rust programming
---

After dealing with a program that unexpectedly printed to stderr when everything was fine, I had an idea for a utility to make the next time easier. It should wrap another program and print its stdout in green, and stderr in red.

I've also been wanting to play with Rust, because of its attitude to performance and distribution. You can only create so many virtualenvs on so many hosts before Python starts to wear thin.

Figured this would be a good way to see whether the grass really is greener. After some futzing around with lifetimes... maybe not. The most interesting bug was from taking a `std::process:ChildStdout` by value, calling `.as_raw_fd` by reference in the same expression, leaving the `ChildStdout` without a reference and therefore dropping it, and it closed the file descriptor on drop. So it was just closed as soon as I wanted to use it.

The project could really use some tests and probably some modularity, but the perfect is the enemy of the good and all that. Maybe I'll come back to it.

Thanks heaps to the friendly folks in the [UQ Computing Society's](https://uqcs.org/) #rust channel in Slack for unblocking me twice.

**Anyway, here's [prettypipe](https://github.com/d-lord/prettypipe/).**

![Screenshot of a curl command, with stdout in green and stderr in red](/assets/images/prettypipe-full.png)
