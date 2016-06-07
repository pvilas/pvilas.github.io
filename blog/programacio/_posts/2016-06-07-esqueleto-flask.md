---           
layout: post
title: Esqueleto para Flask
date: 2016-06-07
author: Pere Vilas
---

Un esqueleto para [Flask](http://flask.pocoo.org/Flask), con logger, plantilla, etc...

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from flask import Flask
from flask import render_template
import logging
from logging.handlers import RotatingFileHandler
app=Flask(__name__)
```
