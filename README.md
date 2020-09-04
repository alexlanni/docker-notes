# Docker Notes

## Mapping degli utenti tra host e container

docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    php:7.2-alpine composer require psr/log
