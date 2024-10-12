# Ejercicio 2

## Parte 1

```bash
docker volume create --name volumenDocker
docker run -d -p 81:80 --name=nginx-1 --mount type=volume,source=volumenDocker,target=/usr/share/nginx/html nginx
docker exec -it nginx-1 /bin/bash -c "echo 'hola' > /usr/share/nginx/html/index.html"
docker run -d -p 82:80 --name=nginx-1 --mount type=volume,source=volumenDocker,target=/usr/share/nginx/html nginx
```

## Parte 2

```bash
docker network create redDocker
docker run -d -ti --name Ubuntu1 --network redDocker ubuntu /bin/bash
docker run -d -ti --name Ubuntu2 ubuntu /bin/bash
docker exec Ubuntu2 apt-get update
docker exec Ubuntu2 apt-get install -y iputils-ping
docker exec Ubuntu2 ping Ubuntu1
docker network connect redDocker Ubuntu2
docker exec Ubuntu2 ping Ubuntu1
```