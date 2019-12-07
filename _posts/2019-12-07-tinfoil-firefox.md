---
title: "Putting a layer of tinfoil onto Firefox"
date:	2019-12-07 00:00:00
excerpt: "Firefox is a lovely browser. But there are a few default behaviours which are tuned for user-friendliness instead of caution."
published: true
---

Firefox is a lovely browser. It's quick, has good extension support, and its developers have a much better attitude to privacy than [its main competitor](https://en.wikipedia.org/wiki/Usage_share_of_web_browsers#/media/File:StatCounter-browser-ww-monthly-200901-201905.png).

There are a few default behaviours which are tuned for user-friendliness instead of caution.



## Disable fallback searches going to Google

If you type anything into the URL bar but it's not a URL, then Firefox will fall back to your default search engine and start a search.

Sometimes you typed into the wrong box, or you typo'd the URL you intended, or you were trying to go directly to a page in your history by typing a couple of keywords but typed too many keywords and it vanished, and now your search engine knows that you were looking for **amazon wedding ring**.

I do this a lot by typing **fa** to go to Facebook, but pressing enter before it catches up and suggests Facebook.

- test that it's currently enabled: open a new tab, and type `foo` into your address bar; if this is enabled, it'll send you to a Google/etc search result for "foo"
- type `about:config` into your address bar and click through the warning (it's OK, you know what you're doing, you're with me!)
- type `keyword.enabled` into the search bar on the page
- in a default install, it's set to the value `true`
- double-click it and the row will change value to `false` and the text will turn bold to show it's been modified
- test it by opening a new tab, typing `foo` into your address bar, and expect a failure result like this:

![Screenshot of Firefox error page: "Please check that the URL is correct and try again."](/assets/firefox_fallback_search_disabled_error.png)

That's better.


## Add search bar in toolbar

We just disabled searches from the address bar, but searches are useful. Let's add a secondary search bar. Older users may remember when this was the default.

Open Firefox's preferences (Windows: **Tools -> Options**, Mac: **⌘+comma**), click Search on the left, and click "Add search bar in toolbar".

![Screenshot of Firefox settings pane and pointing to "Add search bar in toolbar"](/assets/firefox_add_search_bar.png)

Pro tip: if you click the magnifying glass in the search bar, you see those icons at the bottom? You can type a search query and press (Windows: **ctrl+down**, Mac: **opt+down**) to search a specific site.

![Screenshot of Firefox search showing the Wikipedia engine chosen](/assets/firefox_use_one_click_search.png){:.max-width-256}


## Extensions

Get yourself an ad blocker and a password manager. I'm fond of [1Password](https://1password.com) for the latter. Also consider [Privacy Badger](https://addons.mozilla.org/en-US/firefox/addon/privacy-badger17/) by the Electronic Frontier Foundation if you don't like click tracking.

The [Facebook Container](https://addons.mozilla.org/en-US/firefox/addon/facebook-container/) extension by Mozilla is also great for keeping your Facebook identity only to Facebook sites (so they can't track you around the web).
