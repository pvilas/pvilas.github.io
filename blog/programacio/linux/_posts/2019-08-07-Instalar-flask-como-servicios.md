---           
layout: post
title: Instalar web apps Flask como servicios de Linux (Ubuntu)
date: 2019-08-07
tags: linux, flask
---

Es un tema repetitivo el instalar web apps hechas en Flask como servicios de linux, así que he creado esta chuleta como recordatorio.

Está basada en Ubuntu 18.04 LTS, pero se puede adaptar fácilmente a cualquier otra distro.

Primero instalaremos el software que necesitamos tanto en el SO como en el entorno de ejecución. Después configuraremos los servicios.

## Instalación y manejo del software

### Inicio
```
sudo apt update
```
Comprobar que tenemos abiertos los puertos de ssh, http, https y si queremos el de Postgre.

Comprobar versión de python ```python3 -V```. Si es necesario, subirla con ```sudo apt-get upgrade python3```.

Nos hará falta el compilador para instalar uwsgi

```sudo apt install build-essential python3-dev```


### Gestionar servicios
Recordar la sintaxis de systemctl

```sudo systemctl {status, start, stop, restart, reload, enable, disable} nombre_servicio```


### Instalar Nginx
Instalar ```sudo apt install nginx```

Comprobar ```systemctl status nginx```
Si hay errores de configuración ```sudo nginx -t```

Ver página de inicio en http://ip_servidor

Logs en ```/var/log/nginx/errors.log```


## Configurar los servicios

### Directorio del servicio

Cada servicio correrá en su propio directorio donde tendremos instalados los scripts de arranque y el entorno virtual de ejecución python.
```
cd ~
mkdir ejemplo
```

### Creación del entorno de ejecución python
Instalamos virtualenv con ```sudo apt install virtuaenv```.
Creamos el entorno virtual
```
cd ejemplo
virtualevn -p python3 venv
```
Probamos que todo esté ok.
```
. venv/bin/activate
python -V
```

Con el entorno activado, instalamos el uwsgi que necesita las librerías de desarrollo de python3 instaladas anteriormente.

```pip install uwsgi```

### Script de arranque
Los servicios se configuran mediante la creación de un script en ```/etc/systemd/system/ejemplo.service```.

Atención a cada una de las secciones de configuración.

**/etc/systemd/system/ejemplo.service**
```
[Unit]
Description=Nombre descriptivo del servicio
After=network.target

[Service]
User=ubuntu
Group=www-data
Restart=always
RestartSec=5
StartLimitIntervalSec=0
WorkingDirectory=/home/ubuntu/ejemplo
Environment="PATH=/home/ubuntu/paylink/venv/bin"
Environment="FLASK_ENV=development"
Environment="RUN_MODE=PRODUCTION"
Environment="LC_ALL=es_ES.UTF-8"
Environment="LANG=es_ES.UTF-8"
Environment="SQL_DEBUG=0"
LimitNOFILE=50000
ExecStart=/home/ubuntu/ejemplo/venv/bin/uwsgi --ini /home/ubuntu/ejemplo/ejemplo/uwsgi.ini


[Install]
WantedBy=multi-user.target
```

En *Unit* vemos que el servicio se arranca después de network. El StartLimitIntervalSec es el tiempo de retraso. En *Service*, 
el Restart y RestartSec controlan si el servicio debe reiniciarse después de una caida y en qué tiempo de retraso 
entre la caida y el reinicio. WorkingDirectory
es el directorio desde donde se arrancará la aplicación. *Environment* son variables que se exportarán en el proceso.
Es importante en python que el PATH esté mapeado al directorio del intérprete
del entorno virtual. Finalmente, ExecStart es el script de arranque de Flask.

*LimitNOFILE* controla el número de ficheros abiertos simultáneamente, este número suele ser muy bajo por defecto y nos puede crear 
problemas en cualquier momento siendo mejor aumentarlo.

*ExecStart* apunta al puente uwsgi que tendremos en el entorno de ejecución anteriormente creado, el --ini apunta al fichero de inicio 
que lucirá más o menos como en la sección siguiente.

### Configuración del uwsgi
En este fichero controlamos la comunicación entre nginx y la aplicación via uwsgi.

/home/ubuntu/ejemplo/ejemplo/uwsgi.ini
```
[uwsgi]
module = wsgi:app
lazy-apps = true
master = true
processes = 5

socket = /tmp/ejemplo.sock
chmod-socket = 660
vacuum = true

die-on-term = true

logto = /home/ubuntu/ejemplo/ejemplo/log/uwsgi.log
```

Donde los parámetros más importantes son *lazy-apps* que controla el modo en que se cargan las aplicaciones, *processes* que controla el 
número de procesos que se lanzarán simultáneamente y que debe tunearse a mano (más procesos, más memoria pero mejor rendimiento) y
*socket* que es el socket donde en realidad se conectará nginx con uwsgi.

Adicionalmente, podemos cambiar la localización del log de todo lo que hace el uwsgi, es interesante porque podemos localizar 
problemas rápidamente.

### Configuración del nginx




