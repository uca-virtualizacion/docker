# Una ayuda con el ejercicio de la práctica 2 de Docker ;-)

## Volumes:

`docker run -d -80:80 --name=nginx-1 --mount type=volume,source=volumenDocker,target=/usr/share/nginx/html nginx`

## Networks:

`docker run -d -ti --name Ubuntu1 --network redDocker ubuntu /bin/bash`