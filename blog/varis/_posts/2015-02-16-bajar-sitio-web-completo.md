---           
layout: post
title: Bajar sitio web completo con wget
date: 2015-02-16
comments: false
tags: linux
---

Chuleta de *wget* para bajar sitios web, traducida de [linuxjournal](http://www.linuxjournal.com/content/downloading-entire-web-site-wget).

```
$ wget \
     --recursive \
     --no-clobber \
     --page-requisites \
     --html-extension \
     --convert-links \
     --restrict-file-names=windows \
     --domains website.org \
     --no-parent \
         www.website.org/tutorials/html/
```


Este comando baja el web site www.website.org/tutorials/html/.

Las opciones son:

**--recursive**: baja todo el sitio.  
**--domains website.org**: no seguir enlace fuera de website.org.  
**--no-parent**: no seguir enlaces fuera del directorio tutorials/html/.  
**--page-requisites**: bajar todos los elementos que componen la página (imágenes, CSS, etc.).  
**--html-extension**: salvar con la extensión *.html*.  
**--convert-links**: convertir links para que funcionen localmente, off-line.  
**--restrict-file-names=windows**: modificar nombres de fichero para que funcionen tamvién en windows  
**--no-clobber**: no sobreescribir ficheros existentes (en caso de que la bajada se hubiera interrumpido).  

### Bola extra
Una vez bajado, podemos pasarlo a pdf con [htmldoc](http://linuxpoison.blogspot.com.es/2012/05/convert-html-pages-or-websites-to-pdf.html)
