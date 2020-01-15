---           
layout: post
title: Instalar web apps Flask como servicios de Linux (Ubuntu, nginx, uwsgi)
date: 2019-08-07
tags: linux, flask
---

- [Instalación y manejo del software](#instalación-y-manejo-del-software)
  * [Inicio](#inicio)
  * [Gestionar servicios](#gestionar-servicios)
  * [Instalar Nginx](#instalar-nginx)
- [Configurar los servicios](#configurar-los-servicios)
  * [Directorio del servicio](#directorio-del-servicio)
  * [Creación del entorno de ejecución python](#creación-del-entorno-de-ejecución-python)
  * [Configuración del uwsgi](#configuración-del-uwsgi)
  * [Configuración del nginx](#configuración-del-nginx)
  * [Certificados](#certificados)
    + [Autofirmado](#autofirmado)
    + [Letsencrypt](#letsencrypt)
  * [Instalación de la aplicación](#instalación-de-la-aplicaci-n)
  * [Probamos el uwsgi](#probamos-el-uwsgi)
    + [Notas acerca de los mensajes de uwsgi](#notas-acerca-de-los-mensajes-de-uwsgi)
  * [Script de arranque](#script-de-arranque)
    + [Enablar el servicio](#enablar-el-servicio)
- [Post Instalación](#post-instalación)
  * [Comprobación de la zona horaria](#comprobación-de-la-zona-horaria)


Es un tema repetitivo el instalar web apps hechas en Flask como servicios de linux, así que he creado esta chuleta como recordatorio. 

Está basada en Ubuntu 18.04 LTS, pero se puede adaptar fácilmente a cualquier otra distro.

Primero instalaremos el software que necesitamos tanto en el SO como en el entorno de ejecución. Después configuraremos los servicios.

Suelo usar esta estructura de directorio para las aplicaciones Flask:

* ejemplo
  * venv
  * ejemplo
    * uwsgi.ini
    * log
    * static
    * models
    * project
    * lib
    
Todo el proyecto Flask está en *ejemplo/ejemplo*. El entorno virtual está en *ejemplo/venv*. La configuración de uwsgi está en *ejemplo/ejemplo/uwsgi.conf*. En esta chuleta usaremos también los directorios *log* para el log de uwsgi y *static* que es el mismo static de Flask. 

## Instalación y manejo del software

### Inicio
```
sudo apt update
```
Comprobar que tenemos abiertos los puertos de ssh, http, https y si queremos, el de Postgre.

Comprobar versión de python ```python3 -V```. Si es necesario, subirla con ```sudo apt-get upgrade python3```.

Nos hará falta el compilador para instalar uwsgi

```sudo apt install build-essential python3-dev```


### Gestionar servicios
Recordar la sintaxis de systemctl

```sudo systemctl {status, start, stop, restart, reload, enable, disable} nombre_servicio```

Para ver un listado de los módulos

```sudo systemctl list-units```


### Instalar Nginx
Instalar ```sudo apt install nginx```

Comprobar ```systemctl status nginx```
Si hay errores de configuración ```sudo nginx -t```

Ver página de inicio en http://ip_servidor

Logs en ```/var/log/nginx/error.log```


## Configurar los servicios

### Directorio del servicio

Cada servicio correrá en su propio directorio donde tendremos instalados los scripts de arranque y el entorno virtual de ejecución python.
```
cd ~
mkdir ejemplo
```

### Creación del entorno de ejecución python
Instalamos virtualenv con ```sudo apt install virtualenv```.
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

```
sudo apt-get install libpcre3 libpcre3-dev
pip install uwsgi
```

### Configuración del uwsgi
En este fichero controlamos la comunicación entre nginx y la aplicación via uwsgi.

**/home/ubuntu/ejemplo/ejemplo/uwsgi.ini**
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

# socket = :8080
virtualenv = /home/ubuntu/ejemplo/venv
chdir = /home/ubuntu/ejemplo/ejemplo

logto = /home/ubuntu/ejemplo/ejemplo/log/uwsgi.log
```

Donde los parámetros más importantes son *lazy-apps* que controla el modo en que se cargan las aplicaciones, *processes* que controla el 
número de procesos que se lanzarán simultáneamente y que debe tunearse a mano (más procesos, más memoria pero mejor rendimiento) y
*socket* que es el socket donde en realidad se conectará nginx con uwsgi.

Adicionalmente, podemos cambiar la localización del log de todo lo que hace el uwsgi, es interesante porque podemos localizar 
problemas rápidamente. Evidentemente, el directorio debe existir.


### Configuración del nginx
Vamos al directorio de configuración de nginx y editamos el fichero del sitio

**/etc/nginx/sites-available/ejemplo.com**
```

server {
    listen 80;
    server_name ejemplo.ejemplo.com;
    return 301 https://$host$request_uri;
}


server {
	listen 443;

	ssl on;
	ssl_certificate /home/ubuntu/server/ejemplo.ejemplo.cer;
	ssl_certificate_key /home/ubuntu/server/server.key;

	server_name ejemplo.ejemplo.com;
	
	location /static {
    		root /home/ubuntu/ejemplo/ejemplo;
	}	
	
	location / {
		include uwsgi_params;
		uwsgi_pass unix:/tmp/ejemplo.sock;
	}
}
```

En la primera entrada *server* enrutamos las peticiones al 80 hacia el 443. En la segunda configuramos las rutas a los certificados y la ruta estática a los recursos para Flask. Finalmente, en *location /* le decimos a nginx el socket donde debe enrutar las peticiones, es decir, cuando entre una petición a ejemplo.ejemplo.com la enrutará al socket destrás del cuál está el uwsgi. 

Ahora creamos el enlace en sites-enabled

```sudo ln -s /etc/nginx/sites-available/ejemplo.com /etc/nginx/sites-enabled/```

Y recargamos

```sudo systemctl restart nginx```
Ahora podemos probar el sitio. Para mayor seguridad, descomentamos la línea de nginx.conf que puede causar problemas en multisite

```sudo vi /etc/nginx/nginx.conf```

Buscar **server_names_hash_bucket_size** y descomentarla, dejar valor de 64.

Comprobamos sintaxis en las configuraciones

```sudo nginx -t```

Reiniciamos el servicio

```sudo systemctl restart nginx```

Probamos https://ejemplo.com


### Certificados

#### Autofirmado

Si queremos usar un certificado auto firmado podemos hacer

```
sudo mkdir /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
```
Y cambiar la ruta del certificado en /etc/nginx/sites-available/ejemplo.com

#### Letsencrypt

Hay que seguir las instrucciones de ```https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx```. Básicamente nos bajamos y ejecutamos el certbot.

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python-certbot-nginx
sudo certbot certonly --nginx
```

El directorio de instalación de los certificados es ```/etc/letsencrypt/live/ejemplo.com```. Una vez terminado todo, comprobar que el certificado se autorenovará con

```sudo certbot renew --dry-run```

Editamos ejemplo.com para que busque los certificados de letsencrypt

**/etc/nginx/sites-available/ejemplo.com**
```
...
ssl on;
ssl_certificate /etc/letsencrypt/live/ejemplo.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/ejemplo.com/privkey.pem; 
...

```

Comprobamos todo

```
sudo nginx -t
sudo service nginx restart
```

### Instalación de la aplicación

En este caso, primero instalamos los requerimientos para el driver de postgre y de weasyprint. Después clonamos el proyecto desde github.

```
sudo apt-get install libpq-dev
sudo apt-get install build-essential python3-dev python3-pip python3-setuptools python3-wheel python3-cffi libcairo2 libpango-1.0-0 libpangocairo-1.0-0 libgdk-pixbuf2.0-0 libffi-dev shared-mime-info
cd ~/ejemplo
git clone https://github.com/ejemplo/ejemplo
. venv/bin/activate
pip install Psycopg2
pip install weasyprint
pip install -r requirements.txt
```

Una vez hecho esto podemos arrancar la aplicación en local para ver si todo está correcto.

```
# database connection string
export SQLALCHEMY_DATABASE_URI="postgresql+psycopg2://user:pass@localhost:5432/dbname"

# primera vista después de login
export SECURITY_POST_LOGIN_VIEW='/ejemplo/'

# log level critical, error, warning, info, debug
export LOG_LEVEL=DEBUG
export LOG_ROOT_NAME='ejemplo'
export LOG_FILE=./log/error.log

# development server
export HOST='0.0.0.0'
export PORT=5555
export USE_RELOADER=1

# información de la empresa
export COMPANY_NAME='My company'
export APP_NAME='My app name'

# requerido por weasyprint
export LC_ALL=es_ES.UTF-8
export LANG=es_ES.UTF-8

export RUN_MODE=DEVELOPMENT

export FLASK_APP=ejemplo.py
flask
```

### Probamos el uwsgi
Ahora lanzamos el uwsgi, con el entorno activado, para probar:

```/home/ubuntu/ejemplo/venv/bin/uwsgi --ini /home/ubuntu/ejemplo/ejemplo/uwsgi.ini```

Comprobaremos que esté todo correcto en ```./log/uwsgi.log```.

#### Notas acerca de los mensajes de uwsgi

Puede que el log nos muestre varios mensajes de advertencia, entre ellos:

1. *** Python threads support is disabled. You can enable it with --enable-threads ***
2. !!! no internal routing support, rebuild with pcre support !!!

El primero nos indica que si arrancamos un threads desde un proceso, estos se bloquearán mientras no se suelte el main thread. Por lo tanto, si usamos threads es mejor usar la opción `--enable-threads`, si no los usamos es mejor no activarla por temas de rendimiento.

El segundo indica problemas con el build del wsgi. Posiblemente no se insalaron las librerías libpcre antes del build. Hay que reinstalar y recompilar con `--no-cache`.

```
sudo apt-get install libpcre3 libpcre3-dev
pip install uwsgi -I --no-cache-dir
```

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
Environment="PATH=/home/ubuntu/ejemplo/venv/bin"
Environment="FLASK_ENV=development"
Environment="RUN_MODE=PRODUCTION"
Environment="LC_ALL=es_ES.UTF-8"
Environment="LANG=es_ES.UTF-8"
Environment="SQL_DEBUG=0"
Environment="SQLALCHEMY_DATABASE_URI=postgresql+psycopg2://user:pass@dbserver:5432/dbname"
Environment="SECURITY_POST_LOGIN_VIEW=/example/" # first page after login
Environment="LOG_LEVEL=WARNING"
Environment="LOG_ROOT_NAME=example"
Environment="COMPANY_NAME=My company"
Environment="APP_NAME=my app name"
Environment="LOG_FILE=/var/log/ejemplo.log"
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

*ExecStart* apunta al puente uwsgi que tendremos en el entorno de ejecución anteriormente creado, el --ini apunta al fichero de inicio que lucirá más o menos como en la sección siguiente.

Tenemos que crear y cambiar permisos del fichero de log de la aplicación *LOG_FILE*. Notar que el log del uwsgi va por otra parte.

```
sudo touch /var/log/ejemplo.log
sudo chmod 666 /var/log/ejemplo.log
```

**Nota**

Si estamos instalando otro tipo de servicio, posiblemente User y Group seran `ubuntu` y el ExecStart será parecido a:

```ExecStart=/bin/sh /home/ubuntu/aplicacion/run.sh````



#### Enablar el servicio
Para que el script arranque al principio

```sudo systemctl enable /etc/systemd/system/ejemplo.service```

Arrancamos ahora con

```sudo systemctl start ejemplo.service```

Y comprobamos errores en el log de uwsgi

```tail -n 50 /home/ubuntu/ejemplo/ejemplo/log/uwsgi.log```

en el de nginx

```tail -n 50 /var/log/nginx/error.log```

en el de sistema

```tail -n 50 /var/log/syslog```

y en el de aplicación

```tail -n 50 /var/log/ejemplo.log```

## Post-instalación

### Comprobación de la zona horaria

Comprobamos la zona horaria con `timedatectl`, si no está correcta, la fijamos con

```sudo timedatectl set-timezone Europe/Madrid```

Y volvemos a pasar `timedatectl` para comprobar.
