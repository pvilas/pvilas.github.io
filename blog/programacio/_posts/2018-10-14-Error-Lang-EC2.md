---           
layout: post
title: Solucionar error de language en nuevas instáncias Ubuntu EC2 AWS
comments: false
tags: aws ubuntu
---

Hay que añadir esta linea a ```/etc/environment``` y rebotear.

```
# add this line to /etc/environment; then reboot 
LC_ALL="en_US.UTF-8"
```
