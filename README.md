# Docker-Container-Php-MySQL-Apache
Imagem do Docker configurado php 7.4 MySQL 5.7 e Apache 2.4.32

### Intro
```
/php-apache-mysql/
├── apache
│   ├── Dockerfile
│   └── demo.apache.conf
├── docker-compose.yml
├── .env
├── php
│   └── Dockerfile
└── public_html
    └── index.php
    └── info.php
```

#### index.php
No index.php tem apenas um teste de conexão MySQL para informar ao Dev se o container MySQL esta funcionando.

#### .env
Toda coniguração de variaveis de ambiente deste container está neste env, aqui vc configura a versão do PHP, MyDQL e Apache que você irá criar a imagem.

Abaixo Documentação Técnica:

#### info.php
Irá te apoiar a ver a versão do PHP do Container que esta rodando

#### apache/Dockerfile
```
FROM httpd:2.4.33-alpine

RUN apk update; \
    apk upgrade;
```

#### php/Dockerfile
```
FROM php:7.2.7-fpm-alpine3.7

RUN apk update; \
    apk upgrade;

RUN docker-php-ext-install mysqli
```

#### docker-compose.yml
```
version: "3.2"
services:
  php:
    build: 
      context: './php/'
      args:
       PHP_VERSION: ${PHP_VERSION}
    networks:
      - backend
    volumes:
      - ${PROJECT_ROOT}/:/var/www/html/
    container_name: php
  apache:
    build:
      context: './apache/'
      args:
       APACHE_VERSION: ${APACHE_VERSION}
    depends_on:
      - php
      - mysql
    networks:
      - frontend
      - backend
    ports:
      - "80:80"
    volumes:
      - ${PROJECT_ROOT}/:/var/www/html/
    container_name: apache
  mysql:
    image: mysql:${MYSQL_VERSION:-latest}
    restart: always
    ports:
      - "3306:3306"
    volumes:
            - data:/var/lib/mysql
    networks:
      - backend
    environment:
      MYSQL_ROOT_PASSWORD: "${DB_ROOT_PASSWORD}"
      MYSQL_DATABASE: "${DB_NAME}"
      MYSQL_USER: "${DB_USERNAME}"
      MYSQL_PASSWORD: "${DB_PASSWORD}"
    container_name: mysql
networks:
  frontend:
  backend:
volumes:
    data:
```
IMPORTANTE:

- Para configurar o php.ini do container em questão você deverá acessar o diretório:
```  
    lib/php/7.4
```
file:///home/elias/Imagens/dir.png![image](https://user-images.githubusercontent.com/14336962/131772123-98d1c6e1-dd71-4387-a8a0-a6bcbecba99c.png)
