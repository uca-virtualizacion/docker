# Ejercicio 1

```bash
docker pull httpd

docker run -ti -d -p 8082:80 --name apacheserver_p1 httpd

docker exec -ti apacheserver_p1 /bin/bash

apt-get update

apt-get install nano

nano /usr/local/apache2/htdocs/index.html
```