---
layout: post
title: Service worker?
---
I've spent an afternoon to understand what is service worker for [bug 1145498](https://bugzilla.mozilla.org/show_bug.cgi?id=1145498), and I found it's not only a worker, it's also a proxy between client and server!

Some notes after reading this [introduction](http://www.html5rocks.com/en/tutorials/service-worker/introduction/):

- External events: "install", "fetch", "message", and "activate"  
    Fetch is the one which makes it a proxy, service worker will receive fetch events for everything on its registered location. Which it can leverage cache API to keep a local response.
- Works only for https  
    Can only register for service workers on pages served over HTTPS since you don't want any pieice of code can do strange things to your request.
- Can access IndexedDB API
- Possible overhead  
    1. IPC
    2. Accessing cache
    3. Cloning request/response
