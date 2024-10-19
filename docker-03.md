---
marp: true
title: Prácticas de Docker 03
description: Asignaturas del grado en Ingeniería Informática 
---

<!-- size: 16:9 -->
<!-- theme: default -->

<!-- paginate: false -->
<!-- headingDivider: 1 -->

<style>
h1 {
  text-align: center;
}
img[alt~="center"] {
  display: block;
  margin: 0 auto;
}
</style>

# DOCKER - Parte 3

![width:480 center](img/docker-010.png)

---

## Recordatorio...


`docker ps` Contenedores creados en ejecución

`docker ps -a` Contenedores creados (incluye los que están parados)

`docker container ls`  (con o sin -a)

`docker run NOMBRE_IMAGEN` Crear un contenedor (si no tiene la imagen en local, la descarga)

`docker image ls` Ver las imágenes disponibles en local 


Docker da un nombre aleatorio a los contenedores si no lo elegimos nosotros. Todos los contenedores tienen un ID

```console
$ docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS                      PORTS     NAMES
c8bb700e5f39   hello-world              "/hello"                 5 seconds ago   Exited (0) 4 seconds ago              busy_haibt
efe18fd91e7b   dodero/myserver:latest   "/bin/sh -c 'apache2…"   20 hours ago    Exited (130) 20 hours ago             myserver
```

---

`docker rm busy_haibt` Para borrar contenedores

`docker rm c8b` (no hace falta el ID completo)

Elegir el nombre del contenedor (debe ser único en local):

`docker run --name=hola hello-world`

Crear un contenedor asignando un puerto random en local:

`docker run -d -P --name=apache-server bitnami/apache`

`docker ps -a` (para ver puerto asignado)

Ejecutar terminal en el contenedor creado:

`docker exec -it apache-server /bin/bash`

---

Construir una imagen nueva

```bash
mkdir mi-apache
nano index.html
mv index.html mi-apache
nano Dockerfile
```

Dockerfile:

 ```dockerfile
FROM bitnami/apache
COPY index.html /opt/bitnami/apache/htdocs/index.html
```

```bash
docker build -t usuario/mi-apache .
docker run -d -P --name=mi-apache-1 usuario/mi-apache
```

Subir nueva imagen a Docker Hub:

`docker push usuario/mi-apache`

---

Contenedor que usa directorio enlazado (bind):

```bash
docker run -d -P --name=apache-bind-1 \
   --mount type=bind,source=/Users/Usuario/directorio-compartido,target=/app bitnami/apache
```

Contenedor que usa volumen:

```bash
docker run -d -P --name=apache-volume-1 \
   --mount type=volume,source=vol-apache,target=/app bitnami/apache
```

Borrando volumen (se deben borrar también los contenedores que lo usen):

`docker rm apache-volume-1`

---

Crear una nueva red `opencart-network`:

`docker network create opencart-network`

Contenedor que usa una red específica:

```bash
docker run -d --name=mariadb \ 
  -e ALLOW_EMPTY_PASSWORD=yes \
  -e MARIADB_USER=bn_opencart \
  -e MARIADB_DATABASE=bitnami_opencart \
  --net=opencart-network bitnami/mariadb
```

Conectar un contenedor a una red:

`docker network connect opencart-network mariadb`

---

## Docker compose

Configurar múltiples contenedores como servicios cuando necesitamos:

- Arquitectura que involucra más de un componente/servicio funcionando al mismo tiempo
- Pre-configurar para que estos contenedores trabajen juntos

### Características

- Permite desplegar los contenedores en un único host (limitación).
- En el caso de que se quiera desplegar en un cluster hay que utilizar [Docker Swarm](https://docs.docker.com/engine/swarm/)

Nosotros solo trabajaremos con Docker Compose.

---

## Fichero `docker-compose.yml`

Secciones principales del fichero Docker Compose que vamos a utilizar:

- **Services**: servicios de la aplicación: los contenedores que van a ejecutarse.
- **Networks**: configuración de red que van a utilizar los servicios.
- **Volumes**: volúmenes que van a utilizar los servicios para guardar/leer la información.

---

### Ejemplo inicial con Docker compose

Vamos a desplegar una aplicación web hecha con Python/Flask que devuelva en JSON el nombre del usuario

1. Creamos una carpeta donde añadir los siguientes ficheros:

- `requirements.txt`
- `app.py`
- `Dockerfile`
- `docker-compose.yml`

```console
$ mkdir docker-compose-ejemplo1
```

---

2. Creamos el fichero “requirements.txt”

```txt
Flask
Redis<3.0.0
```

3. Creamos el código de la aplicación Python `app.py`:

```python
from flask import Flask, request, jsonify
from redis import Redis
app = Flask(__name__)
redis = Redis(host="redis", db=0, socket_timeout=5, charset="utf-8", decode_responses=True)
@app.route('/', methods=['POST', 'GET'])
def index():
    if request.method == 'POST':
        name = request.json['name']
        redis.rpush('students', {'name': name})
        return jsonify({'name': name})
    if request.method == 'GET':
        return jsonify(redis.lrange('students', 0, -1))
```

---

4. Creamos el fichero `Dockerfile`

```dockerfile
FROM python:3.7.0-alpine3.8
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV FLASK_APP=app.py
CMD flask run --host=0.0.0.0
```

---

5. Creamos el fichero `docker-compose.yml`

```yml
services:
  app:
    build: .
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
  redis:
    image: redis:4.0.11-alpine
```

---

Ficheros necesarios en la carpeta:

```console
$ ls -l docker-compose-ejemplo1/
total 32
-rw-r--r--  1 dodero  staff  184 11 mar 09:19 Dockerfile
-rw-r--r--  1 dodero  staff  481 11 mar 09:19 app.py
-rw-r--r--  1 dodero  staff  196 11 mar 09:19 docker-compose.yml
-rw-r--r--  1 dodero  staff   17 11 mar 09:15 requirements.txt
```

Cambiarse a la carpeta: `cd ~/docker-compose-ejemplo1`

Ejecutar Docker Compose:

`docker compose up`  (añadir `-d` para ejecutar en background)

**NOTA**: *docker-compose* es una herramienta independiente, mientras que *docker compose* es una versión más nueva integrada en Docker CLI que ofrece mejoras y facilidad de uso.

---

Con lo anterior hemos arrancado un contenedor (servicio) `app` con la aplicación en Python y otro contenedor (servicio) `redis` con una base de datos _redis_.

```console
$ docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED         STATUS         PORTS                    NAMES
ff4292697a4c   dodero/flask-redis:1.0   "/bin/sh -c 'flask r…"   5 minutes ago   Up 5 minutes   0.0.0.0:5000->5000/tcp   docker-compose-ejemplo1-app-1
dc5a0762ce04   redis:4.0.11-alpine      "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   6379/tcp                 docker-compose-ejemplo1-redis-1
```

---

Probando el ejemplo (añadiendo nuevo estudiante):

```console
$ curl --header "Content-Type: application/json" \
   --request POST \
   --data '{"name":"Usuario"}' \
   localhost:5000
```

Probando el ejemplo (obteniendo los estudiantes creados): Acceder a `localhost:5000` en el navegador o ejecutando `curl localhost:5000`

```json
[
    "{'name': 'Usuario'}"
]
```

---

## Gestionando aplicaciones con múltiples contenedores

Parar el servicio

`docker compose down`

Visualizar la configuración de la red creada

`docker network ls`

`docker network inspect docker-compose-ejemplo1_default`

Procesos que están ejecutándose:

`docker compose ps`

`docker compose top`

---

Ver los logs generados por estos procesos:

`docker compose logs`

Ejecutar un comando en un contenedor iniciado:

`docker compose exec [nombre_servicio] [comando]`
`docker compose exec app ps`

---

## Más opciones de configuración con Docker Compose

```yml
services:
  app:
    build: 
      context: .
      dockerfile: Dockerfile
      args:
        - IMAGE_VERSION=3.7.0-alpine3.8
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
  redis:
    image: redis:4.0.11-alpine
```

* context: directorio donde se encuentra el Dockerfile
* dockerfile: nombre del Dockerfile
* args: variables de entorno que se pueden pasar al Dockerfile

---

En el fichero `Dockerfile` se pueden usar variables pasadas desde el fichero `docker-compose.yml`:

```dockerfile
FROM python:3.7.0-alpine3.8
...
```

```dockerfile
FROM python:${IMAGE_VERSION}
...
```
---

## Actualización del fichero `docker-compose.yml`:

Cambiamos el puerto de la aplicación a 5555

```yml
services:
  app:
    build: .
    environment:
      - FLASK_ENV=development
    ports:
      - 5555:5000
  redis:
    image: redis:4.0.11-alpine
```

Ejecutamos `docker compose up -d` y sólo se regenera lo necesario (docker compose mantiene los estados)

---

## Escalar servicios

Se pueden arrancar varias instancias de un mismo servicio para así escalarlo

Escalar un servicio con Docker Compose:

`docker compose up -d --scale app=3` Falla, ¿por qué?

```console
$ docker compose up -d --scale app=3
[+] Running 2/4
 ⠿ Container docker-compose-ejemplo1-redis-1  Running                                                                 0.0s
 ⠿ Container docker-compose-ejemplo1-app-3    Running                                                                 0.0s
 ⠹ Container docker-compose-ejemplo1-app-1    Starting                                                                0.2s
 ⠹ Container docker-compose-ejemplo1-app-2    Starting                                                                0.2s
Error response from daemon: driver failed programming external connectivity on endpoint docker-compose-ejemplo1-app-2
(43f832ac334345e85b60a65851c9ac76d250e3498e0699469e066dddd8655ba6): Bind for 0.0.0.0:80 failed: port is already allocated
```

---

Actualizamos el fichero `docker-compose.yml` modificando la redirección de puertos:

```yml
services:
  app:
    build: .
    environment:
      - FLASK_ENV=development
    ports:
      - "80-83:5000"
  redis:
    image: redis:4.0.11-alpine
```

---

Había un conflicto de puertos porque todas las réplicas querían usar el mismo.

Si persisten los conflictos de puertos, comprobar qué puertos están ocupados y cerrar todos los procesos que ocupen los puertos 80-83:

`sudo lsof -i -P | grep LISTEN`

Abrir en el navegador `localhost:80`, `localhost:81`, etc.

Probar a ejecutar el comando con distintos datos (`'{"name":"Ana"}'`, `'{"name":"Belén"}'`...) y distintos endpoints (`localhost:80`, `localhost:81`...). ¿Se comparten los valores almacenados en `redis`? ¿Y si lanzáramos varias instancias del contenedor `redis` con `docker compose up -d --scale app=3 --scale redis=3`?

```bash
curl --header "Content-Type: application/json" \
   --request POST \
   --data '{"name":"Usuario"}' \
   localhost:80
```
---

* No hay una asignación directa entre las instancias de app y redis.

* Docker utiliza un balanceo de carga para distribuir las conexiones entre las instancias escaladas de redis.

* Si necesitas controlar detalladamente la asignación instancias de app a redis (por ejemplo, una instancia de app siempre hablando con una instancia de redis específica), necesitarías un orquestador más avanzado como Kubernetes

---

## Usando volúmenes con Docker Compose

Usando memoria persistente en los contenedores

---

### Ejemplo de uso de volumen con Docker Compose

Configuramos el volumen en el archivo `docker-compose.yml`

```yml
services:
  app:
    build: .
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
  redis:
    image: redis:4.0.11-alpine
    volumes:
      - mydata:/data
volumes:
  mydata:
```

---

Ejecutamos docker compose

`docker compose down`

`docker compose up -d`

Consultamos el volumen creado

`docker volume ls`

---

## Configuraciones de red con Docker Compose

Conceptos básicos de red con Docker Compose

---

### Ejemplo de configuración de red con Docker Compose

Configuramos la red en el archivo `docker-compose.yml`

```yml
services:
  app:
    build: .
    environment:
      - FLASK_ENV=development
    ports:
      - 5000:5000
    networks:
      - mynet
  redis:
    image: redis:4.0.11-alpine
    volumes:
      - mydata:/data
    networks:
      - mynet
volumes:
  mydata:
networks:
  mynet:
```

---

Ejecutamos docker compose

`docker compose down`

`docker compose up -d`

Ver la configuración de la red creada

`docker network ls`
`docker network inspect docker-compose-ejemplo1_mynet`
