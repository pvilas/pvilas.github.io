---
layout: post
title: ubuntu resolver problemas con cron
author: Pere Vilas
tags:
- ubuntu
---

Para programar tareas, suelo usar scripts en `/etc/cron.daily`, weekly, hourly, etc. Los logs de estos procesos quedan en '/var/log/syslog'
y podemos consultarlos con 'grep CRON /var/log/syslog'. 

Si alguno de los procesos en el script falla, no tendremos información a no ser que redirijamos la salida a un fichero de log específico.

Una forma más rápida de hacer debug sería ejecutar el script como usuario *root* para ver la salida en la consola.

```sudo runuser -l root -c '/etc/cron.daily/script.sh'```
