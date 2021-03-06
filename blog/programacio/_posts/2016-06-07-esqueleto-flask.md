---           
layout: post
title: Esqueleto para Flask
date: 2016-06-07
author: Pere Vilas
---

Un esqueleto para [Flask](http://flask.pocoo.org/Flask), con logger, plantilla, etc...

prova.py


```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from flask import Flask
from flask import render_template
import logging
from logging.handlers import RotatingFileHandler
app=Flask(__name__)

@app.route("/")
def arrel():
    return "Hello World!"


@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)


@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    app.logger.warning('A warning occurred (%d apples)', 42)
    app.logger.error('An error occurred')
    app.logger.info('Info')
    return 'User %s' % username

if __name__=="__main__":
    formatter = logging.Formatter(
                 "[%(asctime)s] %(pathname)s:%(lineno)d} %(levelname)s - %(message)s")
    handler=RotatingFileHandler('logs/foo.log', maxBytes=10000, backupCount=1)
    handler.setLevel(logging.DEBUG)
    handler.setFormatter(formatter)
    # gravar també els logs de werkzeug
    log = logging.getLogger('werkzeug')
    log.setLevel(logging.DEBUG)
    log.addHandler(handler)
    app.logger.addHandler(handler)
    app.run()
    
```

### plantilla (en directorio templates)

```html
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```

### arranque servidor flask con opciones

```
export FLASK_APP=prova.py
export FLASK_DEBUG=1
flask run
```
