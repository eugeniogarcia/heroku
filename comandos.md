# Introducción

Estos son los comandos básicos para desplegar la primera aplicación en Heroku.

## Login

Login en heroku

```ps
heroku login
```

## Crear una aplicación

Crea una aplicación en heroku

```ps
heroku create

Creating app... done, floating-ocean-97551
https://floating-ocean-97551.herokuapp.com/ | https://git.heroku.com/floating-ocean-97551.git
```

Si hemos lanzado el comando anterior dentro de un repositorio git, se actualiza, de modo que cuando hacemos push lo estará haciendo al repositorio de la aplicación creada. Podemos fijarnos por ejemplo en el caso anterior, que el repositorio es `https://git.heroku.com/floating-ocean-97551.git`.

Podemos hacer cambios en la aplicación, compilarla, y luego hacer un push al repositorio. Esto desplegara la aplicación en heroku - porque por defecto está configurada para que los despliegues se hangan con el heroku CLI. También se puede conectar la aplicación heroku a un repositorio de git, o al registro de contenedores de Heroku. En estos dos casos el despliegue se lanzaría al hacer push al repositorio, o publicar una nueva imagen en el registro:

```ps
mvn clean install

git stage .

git commit -m "primera version"

git push heroku master
```

Una vez desplegada la aplicación la podemos escalar. Por ejemplo, para que haya una instancia:

```ps
heroku ps:scale web=1
```

Podemos abrir la aplicación como sigue:

```ps
heroku open
```

o abrirla en un recurso concreto:

```ps
heroku open hello
```

## Ver logs

Podemos ver los logs - en streaming en tiempo real - como sigue:

```ps
heroku logs --tail
```

## Dynos. Escalado

Podemos ver el número de instancias que tenemos corriendo:

```ps
heroku ps

Free dyno hours quota remaining this month: 987h 28m (98%)
Free dyno usage for this app: 0h 0m (0%)
For more information on dyno sleeping and how to upgrade, see:
https://devcenter.heroku.com/articles/dyno-sleeping

=== web (Free): java -jar target/java-getting-started-1.0.jar (1)
web.1: up 2019/07/08 17:42:57 -0500 (~ 7s ago)
```

Podemos escalar:

```ps
heroku ps:scale web=1
```

O volver a cero:

```ps
heroku ps:scale web=0
```

Las instancias son serverles. Si no hay actividad en un determinado tiempo, la instancia se para. Nuevas peticiones harían que la instalación arrancase de nuevo, pero hay un tiempo de arranque involucrado.


## Desarrollo local

Para arrancar la instancia en local podemos hacer:

```ps
heroku local web
```

o 

```ps
heroku local start
```

## Variables de Entorno. Configuración

Podemos ver la configuración de variables de entorno como sigue:

```ps
heroku config


=== floating-hollows-59229 Config Vars
DATABASE_URL: postgres://emsarxlhgliqeq:a3011ac9f77fd0f922eccba50079d64253817497bbf62b16251c9d4b8bb40198@ec2-34-204-22-76.compute-1.amazonaws.com:5432/dbgbbu99ab439n
ENERGY:       20 GeV
```

Podemos añadir una configuración:

```ps
heroku config:set ENERGY="20 GeV"

Setting ENERGY and restarting floating-ocean-97551... done, v7
ENERGY: 20 GeV
```

En el repositorio hay un archivo `.env` en el que podemos declarar también las variables de entorno:

```
ENERGY=20 GeV
```

## Run-time y Addons

Podemos ver propiedades del contenedor lanzando un Dyno one-off:

```ps
heroku run java -version


Running java -version on floating-ocean-97551... up, run.2026 (Free)
openjdk version "1.8.0_212-heroku"
OpenJDK Runtime Environment (build 1.8.0_212-heroku-b03)
OpenJDK 64-Bit Server VM (build 25.212-b03, mixed mode)
```

Podemos ver todos los addons disponibles:

```ps
heroku addons


Add-on                                      Plan       Price  State
------------------------------------------  ---------  -----  -------
heroku-postgresql (postgresql-curly-33328)  hobby-dev  free   created
 +- as DATABASE

The table above shows add-ons and the attachments to the current app (floating-hollows-59229) or other apps.
```

Podriamos añadir un add-on:

```ps
heroku addons:create papertrail
```

Para usar este add-on:

```ps
heroku addons:open papertrail
```

Podemos ver información detallada de los add-ons como sigue:

```ps
heroku pg


=== DATABASE_URL
Plan:                  Hobby-dev
Status:                Available
Connections:           10/20
PG Version:            12.2
Created:               2020-04-05 17:44 UTC
Data Size:             8.0 MB
Tables:                1
Rows:                  2/10000 (In compliance)
Fork/Follow:           Unsupported
Rollback:              Unsupported
Continuous Protection: Off
Add-on:                postgresql-curly-33328
```

Podemos conectarnos con el postgres, siempre y cuando tengamos postgress instalado en local, usando:

```py
heroku pg:psql
```

## Avanzado

- Cuando se despliega una aplicación, Heroku elige un buildpack y crea un slug
- El slug se despliega con un Dyno
- El `procfile` de la aplicación tiene todos los procesos que la constituyen:

```txt
web: java -jar lib/foobar.jar $PORT
queue: java -jar lib/queue-processor.jar
```

Podemos escalarla:

```ps
heroku ps:scale web=3 queue=2
```

```ps
heroku ps


== web: 'java lib/foobar.jar $PORT'
web.1: up 2013/02/07 18:59:17 (~ 13m ago)
web.2: up 2013/02/07 18:52:08 (~ 20m ago)
web.3: up 2013/02/07 18:31:14 (~ 41m ago)

== queue: `java lib/queue-processor.jar`
queue.1: up 2013/02/07 18:40:48 (~ 32m ago)
queue.2: up 2013/02/07 18:40:48 (~ 32m ago)
```

Podemos ver todas las releases desplegadas en heroku:

```ps
heroku releases


=== floating-hollows-59229 Releases - Current: v8
v8  Set ENERGY config vars                           egsmartin@gmail.com  2020/04/05 20:29:17 +0200 (~ 2h ago)
v7  Deploy f03da402                                  egsmartin@gmail.com  2020/04/05 20:28:25 +0200 (~ 2h ago)
v6  Deploy ecc90abe                                  egsmartin@gmail.com  2020/04/05 20:18:32 +0200 (~ 2h ago)
v5  Deploy 7894b412                                  egsmartin@gmail.com  2020/04/05 19:44:47 +0200 (~ 2h ago)
v4  @ref:postgresql-curly-33328 completed provisio…  egsmartin@gmail.com  2020/04/05 19:44:46 +0200 (~ 2h ago)
v3  Attach DATABASE (@ref:postgresql-curly-33328)    egsmartin@gmail.com  2020/04/05 19:44:46 +0200 (~ 2h ago)
v2  Enable Logplex                                   egsmartin@gmail.com  2020/04/05 19:10:27 +0200 (~ 3h ago)
v1  Initial release                                  egsmartin@gmail.com  2020/04/05 19:10:26 +0200 (~ 3h ago)
```

Podemos conectarnos al contenedor, a un one-off dyno:

```ps
heroku run bash
```

