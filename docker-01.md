---
marp: true
title: Apuntes IISS
description: Apuntes de Implementación e Implantación de Sistemas Software
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

# DOCKER - Parte 1

![width:480 center](img/docker-010.png)

---

<!-- paginate: true -->

## ¿Qué es Docker?

### Algunas definiciones:

1. Un entorno chroot (jaula de dependencias)
2. Contrato entre _sysadmin_  y  desarrollador
3. Empaquetador de aplicaciones
4. Un sistema de virtualización

---

## ¿Qué es Docker?

### Contrato entre _sysadmin_  y desarrollador

El  administrador solo debe  __desplegar__  los contenedores, mientras que el  desarrollador puede trabajar e instalar con ellos sin poner en riesgo el sistema.

#### Empaquetador de aplicaciones

Se puede crear un  _container_ para la aplicación, de modo que se ejecuten igual en distintas máquinas.

---

## ¿Qué es Docker?

#### Un sistema de virtualización

__Virtual machine__: _Include the application, the necessary binaries and libraries, and an entire guest operating system –– all of which can amount to tens of GBs._

![](img/docker-011.png)

---

## Virtualización

__Container__: _Include the application and all of its dependencies –– but share the kernel with other containers, running as isolated processes in user space on the host operating system. Docker containers are not tied to any specific infrastructure: they run on any computer, on any infrastructure, and in any cloud._

![](img/docker-012.png)

---

## Virtualización de Docker

- Docker no solo virtualiza el hardware, también el sistema operativo.

- Docker es una tecnología de _código abierto_ para crear, ejecutar, probar e implementar aplicaciones distribuidas dentro de __contenedores__ de software

- Docker está basado en el sistema de virtualización de __Linux__ 

---

## Contenedores Docker

### ¿Qué es un contenedor?

- Los contenedores crean un __entorno virtual__ para las aplicaciones
- Ocupan  menos __espacio__  que una máquina virtual al no tener que almacenar el sistema completo.
- El tiempo de __arranque__ de un container  es inferior a 1 segundo.
- Para __integrar__ máquinas virtuales en un host, debemos establecer la red. En los contenedores de Docker la integración es directa. 

---

### Ventajas para desarrolladores

- __Dependencias__: Docker permite a los desarrolladores entregar servicios aislados, gracias a la eliminación de problemas de dependencias de ejecución del software.
- __Productividad__: Se  reduce el tiempo empleado en configurar los entornos o solucionar problemas relacionados con los mismos.
- __Despliegue__: Las aplicaciones compatibles con Docker se pueden desplegar desde equipos de desarrollo a versiones de producción.

---

## Docker Hub

 __https://hub.docker.com/__ 

Es un repositorio donde los usuarios de Docker pueden compartir los contenedores que han creado. Además se pueden encontrar repositorios oficiales.

Existe una opción para registro privado, pero es de pago.


## Instalación de Docker

- Trabajaremos con Ubuntu, aunque es posible en cualquier sistema operativo
- Docker solo trabaja con sistemas de 64 bits

---

### Instalación (Ubuntu)

#### Opción 1:

https://docs.docker.com/engine/install/ubuntu/

#### Opción 2:

```
sudo apt-get update
wget -qO- https://get.docker.com/ | sh
```

Comprobamos que se está ejecutando:

```
sudo docker --version
sudo service docker status
```
---

### Instalación (Windows)

- Docker está ya integrado en Windows 10, haciendo uso de Hyper-V
- Cuando se usa Docker no se puede utilizar __Virtual Box__, lo que puede significar un problema
- Tenemos la opción de deshabilitar Hyper-V. Para ello debemos ejecutar como administrador :

Apagar

```
bcdedit /set  hypervisorlaunchtype off
```

Encender

```
bcdedit /set hypervisorlaunchtype auto
```

---

## Usos sin necesidad de _sudo_ (opcional)
https://docs.docker.com/engine/security/rootless/

Creamos un grupo docker 

`sudo groupadd docker`

Añadimos el usuario al grupo

`sudo usermod -aG docker $USER`

Cerramos sesión y volvemos a entrar

---

Comprobamos que se está ejecutando

`sudo service docker status`

Vemos que versión estamos trabajando

`docker --version`

Lista de comandos

`docker`

Información del sistema

`docker info`

Descarga y ejecución de un contenedor básico

`docker run hello-world`

---

## Docker Hub

- Es un repositorio para descargar imágenes.
- Es un repositorio para subir nuestras imágenes (públicas o privadas)
-  Tiene servicios automatizados [webhooks](https://docs.docker.com/docker-hub/webhooks/)
- Se integra con Github y BitBucket

---

## Docker Hub

Darse de alta como usuario

[https://hub.docker.com/](https://hub.docker.com/)

Iniciar sesión desde el terminal

`docker login`

Ver los repositorios locales descargados

`docker images`
> Tendremos el hello-world que hemos utilizado para comprobar que el servicio funcionaba

---

## Descargando de repositorios 

Buscar un repositorio

`docker search ubuntu`

Descargar la versión oficial

`docker pull ubuntu`

> Podemos usar opciones `ubuntu:14.04` `ubuntu:latest`

Ejecutamos el contenedor 

`docker run ubuntu /bin/echo "Pues parece que funciona"`

 > Al ejecutar `run`, si la imagen no existiera en el repositorio local, se descarga

---

## Repositorios en ejecución

Ver los contenedores en ejecución

`docker ps`

> `CONTAINER ID` es el identificador del contenedor
> `IMAGE` es la imagen usada para crearlo
> `NAME` -- si no se especifica, Docker crea un nombre aleatorio

Ver los contenedores que se han creado

`docker ps -a`

Ver el último repositorio creado

`docker ps -l`

Borrar una imagen

`docker rmi [nombre_imagen]`

---

## Trabajando en los contenedores

Poner nombre a un contenedor

`docker run -t -i --name myUbuntu ubuntu /bin/bash`

 `-t` incluye terminal dentro del contenedor
 `-i` se puede trabajar de manera interactiva

Para salir del terminal

`exit`

---

## Trabajando en los contenedores en ejecución

Poner nombre a un contenedor

`docker run --name myUbuntu ubuntu /bin/bash -c "while true; do echo hola mundo; sleep 1; done"`

Vemos los contenedores que se están ejecutando

`docker ps`

Detenemos el contenedor

`docker stop [nombre contenedor]`

Borrar un contenedor

`docker rm [nombre_contenedor]`

---

## Trabajando en los contenedores ejecutando en segundo plano

Poner nombre a un contenedor

```
docker run -d --name myUbuntu ubuntu /bin/bash \
    -c "while true; do echo hola mundo; sleep 1; done"
```

 > **-d** significa que se trabaje en segundo plano_ 

Vemos los contenedores que se están ejecutando: `docker ps`

Podemos ver la salida de nuestro contenedor: `docker logs myUbuntu`

Detenemos el contenedor

`docker stop [nombre contendedor]`

Abrir terminal de un determinado contenedor

`docker exec -it myUbuntu /bin/bash`

---

## Trabajando con Docker

![w:640](img/docker-013.png)

---

## Opciones

Exportar un contenedor

`docker export myUbuntu > myUbuntu.tar`

Importar desde un fichero local

`docker import /path/to/latest.tar`

---

## Ejemplo: contenedor con aplicación web en Python

Descargamos la imagen

`docker run -d -P training/webapp python app.py`

> __-d__ trabajar en segundo plano
> __-P__ mapea los puertos a nuestro host para poder ver la aplicación

Ejecuta con `python` la aplicación web de servidor `app.py`, contenida en la imagen `training/webapp`

Vemos los puertos usados: `docker ps -a`

> La opción **-P** redirecciona los puertos del contenedor de la imagen (en este caso el 5000) a un puerto de nuestro host local: `[0.0.0.0:55000->5000/tcp]`

Abrir en el navegador http://localhost:55000/

---

### Otras opciones de ejecución

Especificando un puerto en el host

`docker run --name myWeb -d -p 80:5000 training/webapp python app.py`

> **-p** mapea el puerto 80 del host local al 5000 del contenedor

Abrir en el navegador http://localhost

Podemos ver los puertos asociados a nuestro contenedor

`docker port [nombre contenedor] puerto`

`docker port myWeb 5000`

Podemos ver los logs de nuestra aplicación

`docker logs -f myWeb`
> **-f** muestra los logs de manera continua

---

Ver procesos que se están ejecutando en el contenedor 

`docker top myWeb`

Inspeccionar la máquina

`docker inspect myWeb`

Detener el contenedor

`docker stop myWeb`

Reiniciar un contenedor

`docker start myWeb`

---

## Creando nuestros contenedores

- Creación __desde imagen__: Partimos de una imagen de un contenedor para realizar modificaciones y crear el nuestro

- Creación desde cero: Partimos de un __fichero de configuración__ para crear una imagen para un contenedor

### Creación desde una imagen

Ejecutar el contenedor `myWeb`

`docker run --name myWeb -t -i  -d -p 80:5000 training/webapp python app.py`

Abrir un terminal del contenedor

`docker exec -t -i myWeb /bin/bash`

---

Instalar nano, editar `app.py` y hacer commit

```bash
docker commit -m "Modificando saludo" -a "Mi Nombre" ac1c3a5c70ad \
    usuario/myweb:v2
```

> - Especificar el id del contenedor en ejecución a modificar
> - `usuario`  debe coincidir con nuestro usuario de Docker Hub
> - El nombre de la imagen `myweb` debe ir en minúsculas

Vemos nuestra nueva imagen

`docker images`

Ejecutar nuestra imagen

```
docker run --name myWeb2 -t -i  -d -p 80:5000 \
    usuario/myweb:v2 python app.py
```

---

### Creación de un _Dockerfile_

https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/

---

## Comandos básicos de Dockerfile

__FROM:__   para definir la imagen base
__MAINTAINER:__    nombre o correo del mantenedor
__COPY:__   copiar un fichero o directorio a la imagen
__ADD:__   para copiar ficheros desde urls
__RUN:__   ejecutar un comando dentro del container
__CMD:__   comando por defecto cuando ejecutamos un container
__ENV:__   variables de entorno
__EXPOSE:__   para definir los puertos del contenedor
__VOLUME:__   para definir directorios de datos que quedan fuera de la imagen
__ENTRYPOINT:__   comando a ejecutar de forma obligatoria al correr una imagen
__USER:__   Usuario para RUN, CMD y ENTRYPOINT
__WORKDIR:__   directorio para ejecutar los comandos

---

# Ejemplo de Dockerfile

Creamos un directorio: `mkdir myapacheweb && cd myapacheweb`

Creamos un fichero llamado Dockerfile: `touch Dockerfile`

Editamos el fichero con el siguiente contenido:

```docker
FROM ubuntu:14.04 
RUN apt-get update && \
  apt-get install -y apache2 && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* 
RUN echo "<h1>Apache with Docker</h1>" > /var/www/html/index.html 
ENV APACHE_RUN_USER www-data 
ENV APACHE_RUN_GROUP www-data 
ENV APACHE_LOG_DIR /var/log/apache2 
EXPOSE 80 
ENTRYPOINT apache2ctl -D FOREGROUND 
```

---

Construcción de nuestra imagen

`docker build -t usuario/myserver .`

Ejecutamos nuestra imagen

`docker run -i -t -p 3333:80 --name myserver usuario/myserver:latest`

Probar el contenedor y abrir http://localhost:3333//

Colocamos nuestra imagen en Docker Hub

`docker push usuario/myserver`

Averiguar el tamaño de las imágenes

`docker history usuario/myserver`

---

## Imagen de Ubuntu

La imagen de ubuntu no trae muchas de las herramientas necesarias para comprobar el funcionamiento del contenedor. Se recomienda instalar las siguientes herramientas.

```bash
apt-get update
apt-get install nano
apt-get install -y net-tools
apt-get install telnet
apt-get install iputils-ping
apt-get install curl
```

---

<style scoped>
p {
  text-align: center;
  font-size: 125%;
  color: green;
}
</style>

[https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)


# Ejercicio: práctica 1

Crear un contenedor con Apache Server. El servidor por defecto muestra en la página principal “It works!”. Modificar este mensaje para que muestre un saludo personal: “Hello + (tu nombre y apellidos)!”. Posteriormente, configurarlo para que por defecto utilice el puerto 8082.

Tareas:

1. Crear contenedor con Apache Server 2 (buscar en Docker Hub)
2. Personalizar el contenedor.
3. Subir la imagen del contenedor creado a Docker Hub. La imagen debe llamarse `apacheserver_p1`.
4. Subir el enlace de la imagen creada a la tarea del CV.

# Task assignment 1

Create a container with Apache Server. The default server displais “It works!” on the main page. Update this message to show a personal greeting: “Hello + (your first and last name)!”. Finally, configure the container to use the port 8082 by default.

Steps:

1. Create a container with Apache 2 (search in Docker Hub website).
2. Customize the container.
3. Upload the created container image to Docker Hub. The image name must be `apacheserver_P1`.
4. Upload the Docker Hub link of your image to the CV task.

