---
layout: post
title: Unifi controller CPU issue (Ubuntu Linux)
author: Pere Vilas
tags:
- linux
---

If you are experimenting issues with the [CPU usage on a unifi controller](https://help.ubnt.com/hc/en-us/articles/115005159588-UniFi-How-to-Tune-the-Controller-for-High-Number-of-UniFi-Devices#related%20articles), you might read the related article on ui support.

Basically, append these lines to `/usr/lib/unifi/data/system.properties` to enable advanced garbage collection and 
increase the memory the java process will use (in Mb). If this space is not enough, java will overuse the CPU causing the app to freeze.

```
unifi.G1GC.enabled=true
unifi.xms=6144
unifi.xmx=6144
```

Then make a `sudo service unifi restart`.

Use `free -h`and `top` to monitor the app. You can also view the server log with

´´´
sudo tail -f /var/log/unifi/server.log
´´´
