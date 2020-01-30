---
layout: post
title: Firefox won't execute your body onload code
---
A few months ago, I decided to move away from Google Chrome and gave Mozilla Firefox another chance. I've been happy with it so far, except for an annoying bug that I encounter all the time and which is the subject of this post. The weird part is I couldn't find anyone reporting the same bug lately, so it might be due to my configuration (or my inability to use Firefox properly!). I know some [Hunter](https://hunter.io) customers encountered it as well. Though, I feel it deserves a post, so I can at least ping [@firefox](https://twitter.com/firefox) :)

<h3>The bug</h3>

This bug is pretty simple: Firefox won't execute any JS code in `<body onload="XXX">`. So let's say you've got the following scenario, which is really common in a payment flow (3D Secure, in particular), when you're being redirected from the merchant to you bank's site to proceed to the challenge:
```
<!DOCTYPE html>
<html>
  <body onload="document.google.submit()">
    <form action="https://www.google.com" name="google">
      <input type="hidden" name="q" value="Hello world" />
    </form>
  </body>
</html>
```

Once the body is loaded, the browser is supposed to submit the form named "google". Every browser will redirect you to <i>https://www.google.com/?q=Hello+world</i> but <b>Firefox won't</b>.

Even a single `alert` won't actually be shown on Firefox:
```
<!DOCTYPE html>
<html>
  <body onload="alert('Hello world')"></body>
</html>
```

<h3>The solution (for Web developers)</h3>

At first, I thought it was affecting only me and was happy to open the Web console to execute manually the function (even thought it didn't create the best frictionless shopping experience ^^). But once we started to get reports from Hunter customers on Firefox telling that they couldn't perform their payment, I started to take it more seriously and simply replaced our implementation with:
```
<!DOCTYPE html>
<html>
  <body>
    <form action="https://www.google.com" name="google">
      <input type="hidden" name="q" value="Hello world" />
    </form>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        document.google.submit()
      })
   </script>
  </body>
</html>
```

This does work on all browsers, including Firefox (up to version 66.0).

So if you're a Web developer and use `<body onload="XXX">` instructions, then I'd definitely recommend you to get rid of them if you want to be on the safe side of things.

<h3>The solution (for Firefox users)</h3>

The best fix so far is to run manually the function to be executed through the Web console, but I'm aware it's not suited for every Firefox user. For this reason, I'm going to report this to the Firefox team, we'll see if they come up with something.

Hopefully I'll have something to add to this post!
