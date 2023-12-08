# Installer wordpress avec docker 

- Créer un repertoire par exemple `mywordpress` et y accéder
  ```
  mkdir mywordpress
  cd mywordpress
  ```
- Créer un fichier `docker-compose.yml` et y ajouter ce code 
  ```
    version: '3.8'
    services:
    db:
        # We use a mariadb image which supports both amd64 & arm64 architecture
        image: mariadb:10.6.4-focal
        # If you really want to use MySQL, uncomment the following line
        #image: mysql:8.0.27
        command: '--default-authentication-plugin=mysql_native_password'
        volumes:
        - db_data:/var/lib/mysql
        restart: always
        environment:
        - MYSQL_ROOT_PASSWORD=somewordpress
        - MYSQL_DATABASE=wordpress
        - MYSQL_USER=wordpress
        - MYSQL_PASSWORD=wordpress
        expose:
        - 3306
        - 33060
    wordpress:
        image: wordpress:latest
        volumes:
        - wp_data:/var/www/html
        ports:
        - 80:80
        restart: always
        environment:
        - WORDPRESS_DB_HOST=db
        - WORDPRESS_DB_USER=wordpress
        - WORDPRESS_DB_PASSWORD=wordpress
        - WORDPRESS_DB_NAME=wordpress
    volumes:
    db_data:
    wp_data:
  ```
- Exécuter le fichier avec la commande `docker-compose up -d`
  
```
mywordpress$ docker-compose up -d
```

- Vérifier après téléchargement que les containers fonctionnes : 
  
```
 mywordpress$ docker ps
```
Le résultat devrait ressembler à ceci
```
 CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                               NAMES
05aa8367727c   wordpress:latest       "docker-entrypoint.s…"   35 minutes ago   Up 35 minutes   0.0.0.0:80->80/tcp, :::80->80/tcp   mywordpress_wordpress_1
2c9261ec4089   mariadb:10.6.4-focal   "docker-entrypoint.s…"   35 minutes ago   Up 35 minutes   3306/tcp, 33060/tcp                 mywordpress_db_1
```
On peut ainsi accéder à wordpress sur [0.0.0.0:80](0.0.0.0:80)
## Références 

* [docs docker sample wordpress](https://docs.docker.com/samples/wordpress/)
* [github docker wordpress](https://github.com/docker/awesome-compose/tree/master/official-documentation-samples/wordpress/)

 ## Problème avec les permissions docker 

 créer un groupe `groupadd`
 ```
 $ sudo groupadd docker
 ```
 ajouter ce groupe parmi les utilisateur du pc 
 
```
$ sudo usermod -aG docker $USER
```
activer les changements
```
$ newgrp docker
```
