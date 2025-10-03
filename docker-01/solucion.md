# Ejercicio 1

```bash
docker pull httpd

docker run -ti -d -p 8082:80 --name apacheserver_p1 httpd

docker exec -ti apacheserver_p1 /bin/bash

apt-get update

apt-get install nano

nano /usr/local/apache2/htdocs/index.html

docker commit -m "Modificando saludo" -a "Nombre Autor" apacheserver_p1 usuario/apacheserver_p1

docker push usuario/apacheserver_p1
```

# Ejercicio 2

```bash
mkdir my-html-folder
touch my-html-folder/index.html
nano my-html-folder/index.html

touch Dockerfile
nano Dockerfile

docker build -t juanantonioch84/my-nginx-content .
docker run --name some-nginx -d -p 1234:80 juanantonioch84/my-nginx-content
```