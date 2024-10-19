---
marp: true
title: Entregable 1 (Docker)
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

# DOCKER - Entregable

![width:480 center](img/docker-010.png)

---

## Instrucciones: Docker compose con servidor y base de datos

El entregable consta de dos partes. En la primera parte se debe crear un fichero `docker-compose.yml` con dos servicios: drupal + mysql. En la segunda parte se debe crear un fichero `docker-compose.yml` con dos servicios: wordpress + mariadb. Se deben usar las imágenes oficiales de cada tecnología.

Deben subirse a la tarea habilitada en el campus virtual los siguientes ficheros:
- Los dos ficheros `docker-compose.yml`
- Un documento Markdown con una documentación de las configuraciones realizadas
- Un video explicativo de entre 5 y 7 minutos en calidad media (720p) donde se explique el funcionamiento de los contenedores. El video debe incluir la explicación de las configuraciones realizadas en cada fichero `docker-compose.yml` y mostrar en el navegador el correcto funcionamiento de los contenedores

---

### Parte 1

1) Crear un fichero `docker-compose.yml` con dos servicios: drupal + mysql.
2) Hacer que el servicio drupal utilice el puerto 81.
3) Hacer que ambos contenedores usen volúmenes para persistir información.
4) Comprobar que puede acceder a `localhost:81` y mostrar la correcta configuración de Drupal.
5) Borrar los contenedores, explicar y demostrar si la información persiste.

---

### Parte 2

1) Crear un fichero `docker-compose.yml` con dos servicios: wordpress +  mariadb.
2) Hacer que el servicio wordpress utilice el puerto 82.
3) Hacer que ambos contenedores usen una red llamada `redDocker`.
4) Comprobar que puede acceder a `localhost:82` y mostrar la correcta configuración de Wordpress.
5) Borrar los contenedores, explicar y demostrar si la información persiste.
