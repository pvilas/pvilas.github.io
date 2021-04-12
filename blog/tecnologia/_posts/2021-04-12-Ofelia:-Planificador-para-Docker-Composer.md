![Ofelia](https://camo.githubusercontent.com/b52908cc8ab9e587745e748b92d998521bc28be00d531e31231f73da53351eda/68747470733a2f2f776569726473706163652e646b2f4672616e636973636f4962616e657a2f47726170686963732f4f66656c69612e676966)

Es muy común que en las aplicaciones web tengamos que realizar tareas programadas por periodos de tiempo. Por ejemplo, enviar
correos electrónicos, hacer backups de la base de datos, etc. 

En el caso de Flask, es extremadamente conveniente crear comandos `cli` que ejecutan estas tareas dentro del entorno completo de la aplicación, es decir, con 
acceso a la configuración, los modelos SQLAlchemy, etc. Ejecutar uno de estos comandos es tan sencillo como, por ejemplo

```
flask enviar-correo
```

Podemos crear tareas **cron** para ejecutar esto con una periodicidad determinada.

Sin embargo, cuando ejecutamos aplicaciones desde containers creados con `docker-compose` las cosas no son tan sencillas. La mayoría de soluciones pasan por conectar
de una u otra forma con el contenedor arrancado y programar a mano las tareas **cron** ajustando permisos, usuarios, etc. Sería mucho mejor tener un solo proceso
separado (microservicio) que se encargara de la ejecución periódica de los comandos.

Para ello está [Ofelia](https://github.com/mcuadros/ofelia), si ¡La secretaria de la T.I.A! que nos permite realizar estas tareas de una forma más desacoplada ya que,
básicamente, se instala como una máquina más, con su propio planificador y usa comandos **exec** para ejecutar comandos en la máquina propia o en otras de la composición.

Lo único que tenemos que hacer es incluir la máquina/servicio ofelia en la composición, poner las dependencias sobre las máquinas que queremos actuar y programar las tareas. 

Toda la información está la página de github, pero os dejo un ejemplo que ejecuta `flask enviar-correo` cada minuto en la misma máquina que está el servicio principal.

```
  myapp:
    build: 
      context: ./server
      dockerfile: Dockerfile    
    command: gunicorn -w 1 -b 0.0.0.0:5000 --timeout 600 --threads 2 --reload project.server:app
    volumes:
      - ./server/:/usr/src/app/
    ports:
      - 5000:5000
    environment:
      - FLASK_ENV=development
      - FLASK_APP=project.server:app
    labels:
      ofelia.enabled: "true"
      ofelia.job-exec.scheluded.schedule: "@every 1m"
      ofelia.job-exec.scheluded.command: "flask enviar-correo"
  
  ofelia:
    image: mcuadros/ofelia:latest
    depends_on:
      - myapp
    command: daemon --docker
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    labels:
      ofelia.job-local.my-test-job.schedule: "@every 20m"
      ofelia.job-local.my-test-job.command: "echo Estoy vivo"      
```
