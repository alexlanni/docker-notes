# Docker Notes

## Mapping degli utenti tra host e container

docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    php:7.2-alpine composer require psr/log


## Per Immagine php:7.X

basta creare l'utente www-data in host con ID 33

## Processo di build

Creazione immagine primitiva:

docker build -t lab.alessandrolanni.net:5050/studio/basic-app/primitive -f Dockerfile .
docker push lab.alessandrolanni.net:5050/studio/basic-app/primitive:latest

Creazione immagine completa (build, primitiva + codice sorgente)

docker build -t lab.alessandrolanni.net:5050/studio/basic-app/build:1.0 -f Dockerfile.build  .
docker push lab.alessandrolanni.net:5050/studio/basic-app/build:1.0

## Per esecuzione in sviluppo

Usare il docker-compose.yml

version: "2"
services:

  basicapp:
    # build:
    #  context: .
    #  dockerfile: Dockerfile
    image: lab.alessandrolanni.net:5050/studio/basic-app
    ports:
      - "8080:80"
    volumes:
      - .:/var/www
      - ./common:/common
    networks:
      - frontend

networks:
  frontend:
    driver: bridge
    
## Per simulare il deploy

1 - Creare il volume:

docker volume create test-common

2 - Avviare il container partendo dall'immagine di build:

docker run --name test-build -v test-common:/common -p 8081:80 --detach lab.alessandrolanni.net:5050/studio/basic-app/build:1.0
