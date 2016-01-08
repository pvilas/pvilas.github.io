---
layout: post
title: Migrar a github pages desde Blogger
date: '2015-12-31T15:22:00.001+01:00'
author: Pere Vilas
tags:
- css
modified_time: '2015-12-31T15:22:08.044+01:00'
blogger_id: tag:blogger.com,1999:blog-9191328551308072706.post-599849715096005937
blogger_orig_url: http://www.pvilas.com/2015/12/migrar-github-pages-desde-blogger.html
---

Crear un blog en <a href="http://github.com/" target="_blank">github</a> es realmente sencillo.

Lo primero es decidirnos por uno de los varios proyectos que nos facilitarán la tarea: Yo he escogido <a href="http://pixyll.com/" target="_blank">pixyll</a> porque me parece muy de estilo "content first", es decir, prima la visualización del contenido. No puede ser más fácil de instalar ya que nos lleva ya hecho el servidor jekyll y las página de estilo usando el rapidísimo <a href="http://www.basscss.com/" target="_blank">basscss</a>.<br /><br />

Si por ejemplo nuestro nombre de usuario en github es <i>usuario</i> nos vamos al proyecto <a href="https://github.com/johnotander/pixyll" target="_blank">pixyll</a> y le hacemos un fork (el botón está arriba a la derecha). Esto copiará el proyecto en nuestra cuenta de github. Ahora nos vamos a nuestra cuenta de github-&gt;Repositories, seleccionamos pixyll y le cambiamos el nombre entrando en Settings-&gt;Repository name.<br /><br />El nombre del repositorio debe ser: <i>usuario</i>.github.io<br /><br />Ya podemos entrar en el sitio web http://<i>usuario</i>.github.io recién creado.<br /><br />Las entradas se hacen en formato <a href="https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet" target="_blank">markdown</a>, para crear una navegamos en proyecto a _posts, aqui creamos un nuevo archivo cuyo nombre debe seguir esta sintaxis:<br /><br />YYY-MM-DD-titulo-de-la-entrada.md<br />

Editamos el fichero en markdown, pero la cabecera siempre debe ser del tipo

<pre>
—
layout: post    
title: titulo de la entrada    
—    
bla bla bla    
</pre>

Con el fichero creado, le damos a commit y, después de unos segundos, ya tendremos la nueva entrada en nuestro blog!!. También podemos tener una copia del blog en nuestra máquina local y sincronizarla con github.io. Para ello sólo tenemos que clonar en nuestro ordenador el repositorio

``` 
git clone http://github.io/usuario/usuario.github.io
```

Podemos arrancar el servidor jekyll para ver el resultado, en el directorio usuario.github.io hacemos

```
gem install github-pages
jekyll serve --watch
```

Para probar vamos a http://localhost:4000/

Con nuestro flamante nuevo blog en marcha, ahora tenemos que **migrar los contenidos de blogger a github**. 

Para exportar las entradas de Blogger, primero obtenemos el RSS desde una dirección de este tipo (donde blogID es el id de nuestro blog):

```
https://www.blogger.com/feeds/blogID/posts/default?published-min=2008-03-16T00:00:00&published-max=2008-03-24T23:59:59&max-results=500
```

Y lo guardamos en un fichero xml. La [sintaxis de consulta blogger](https://developers.google.com/blogger/docs/2.0/developers_guide_protocol) está bien explicada en google.

Después, seguimos este [procedimiento para importar desde blogger a jekyll](http://import.jekyllrb.com/docs/blogger/).

Una vez que comprobemos que todo está perfecto,  añadiremos las nuevas entradas al blog. Para ello entramos en el directorio de _posts y 

<pre>
git add *
git commit -a -m "Migracion de entradas desde blogger"
git push
</pre>

Lo que copiará todas las entradas antiguas al nuevo blog en github. A partir de ahora, ya podremos editar nuestras entradas en markdown, sin depender del horroroso entorno de blogger. Feliz blogeo!.