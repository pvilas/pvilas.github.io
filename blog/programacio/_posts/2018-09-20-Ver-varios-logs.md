---           
layout: post
title: Ver logs dinámicamente
comments: false
tags: ubuntu
---

Para ver uno o varios logs dinámicamente, podemos pasar la opción ```-f``` a tail, lo cuál nos mostrará dinámicamente el final del fichero.

Ejemplo:
```tail -f /var/log/nginx/access.log```

También se puede pasar más de un fichero a la vez, y nos los mostrará separados,

```
tail -f /var/log/nginx/access.log /var/log/nginx/error.log
==> /var/log/nginx/access.log <==

==> /var/log/nginx/error.log <==
```

Muy útil para debugger y supervisión.
