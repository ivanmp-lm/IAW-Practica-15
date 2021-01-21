---
description: >-
  En esta práctica se usará docker y docker compose para lanzar un servidor
  apache con Wordpress.
---

# Práctica 15 - Wordpress con docker compose

El primer paso para empezar a trabajar con docker compose será instalar el propio docker, así que se lanzarán los siguientes comandos para obtenerlo:

```text
$ sudo apt update
$ sudo apt install docker.io
$ sudo apt install docker-compose
```

En esta práctica será necesario lanzar tres servicios \(o tres contenedores\), uno con Wordpress \(y apache\) otro con MySQL como base de datos y el último con phpMyAdmin. Para lanzar los tres contenedores a la vez se creará un archivo llamado "docker-compose.yaml" que tendrá el siguiente contenido:

```text
version: '3.4'


services:
  mysql:
    image: mysql:5.7.28
    ports: 
      - 3306:3306
    environment: 
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    volumes: 
      - mysql_data:/var/lib/mysql
    networks:
      - backend-network

  phpmyadmin:
    image: phpmyadmin
    ports:
      - 8080:80
    depends_on:
      - "mysql"
    environment: 
      - PMA_ARBITRARY=1
    networks:
      - backend-network
      - frontend-network

  wordpress:
    image: wordpress
    ports: 
      - 80:80
    depends_on:
      - "mysql"
    environment: 
      - WORDPRESS_DB_NAME=${WORDPRESS_DB_NAME}
      - WORDPRESS_DB_USER=${WORDPRESS_DB_USER}
      - WORDPRESS_DB_PASSWORD=${WORDPRESS_DB_PASSWORD}
    volumes:
      - wordpress_data:/var/www/html
    networks:
      - backend-network
      - frontend-network

volumes:
  mysql_data:
  wordpress_data:

networks:
  backend-network:
  frontend-network:
```

En ciertas partes del archivo se referencian diferentes variables \(indicado con "${VARIABLE}\) que estarán escritas en un fichero a parte con la extensión ".env" para facilitar el cambio de ciertas opciones sin tener que editar el archivo principal. Su contenido será el siguiente:

```text
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=wp_database
MYSQL_USER=wp_user
MYSQL_PASSWORD=wp_password
WORDPRESS_DB_NAME=wp_database
WORDPRESS_DB_USER=wp_user
WORDPRESS_DB_PASSWORD=wp_password
```

Ambos archivos están disponibles en este mismo repositorio. Después se iniciarán los contenedores utilizando el siguiente comando:

```text
$ sudo docker-compose up -d
```

Dará el siguiente resultado:

![](https://raw.githubusercontent.com/ivanmp-lm/IAW/master/.gitbook/assets/image%20(21).png)

Utilizando el puerto 80 se podrá acceder a wordpress:

![](https://raw.githubusercontent.com/ivanmp-lm/IAW/master/.gitbook/assets/image%20(20).png)
