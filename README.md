# Docker Image for Apache + PHP + Imagick
Original repo https://github.com/huedaya/php-imagick

## How to run?
Just as simple as run `docker-compose up -d` and you're ready to go on http://localhost:8080

## Where to place my app files 
put your PHP's file on `/app/` directory

## Docker Compose example
This the following example of `docker-compose.yml` file
```yaml
version: "3.8"

services:
  app:
    build:
      context: .
      dockerfile: ./docker/server/Dockerfile
    container_name: ${APP_CONTAINER_NAME}
    mem_limit: 300m
    volumes:
      - ./:/var/www/html/
      - composer_cache:/root/.composer/cache
      - yarn_cache:/usr/local/share/.cache/yarn/v6
      - ./docker/server/apache/sites-enabled:/etc/apache2/sites-enabled
      - ./docker/server/php/php.ini:/usr/local/etc/php/conf.d/extra-php-config.ini
    ports:
      - ${APP_HTTP_PORT}:80
      - ${APP_HTTPS_PORT}:443
    networks:
      - local
      - proxy

  db:
    image: mariadb
    container_name: ${DB_HOST}
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}
      TZ: ${TIMEZONE}
    ports:
      - ${DB_HOST_PORT}:${DB_CONTAINER_PORT}
    networks:
      - local
    volumes:
      - db-data:/var/lib/mysql
    command: mysqld --default-storage-engine=MyISAM

networks:
  local:
  proxy:
    external: true

volumes:
  db-data:
  composer_cache:
  yarn_cache:

```

