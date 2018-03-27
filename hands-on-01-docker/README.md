# Create from Docker

```sh
$ docker pull wordpress

Using default tag: latest
latest: Pulling from library/wordpress
2a72cbf407d6: Already exists
273cd543cb15: Pull complete
9106e19b56c1: Download complete
...
Digest: sha256:1cafe6df0e42510840839c8ee2b7330d9b7fd80397f6add28450fff8bf60adf4
Status: Downloaded newer image for wordpress:latest

```

```sh
$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
2a72cbf407d6: Already exists
8181cde51c65: Pull complete
...
Digest: sha256:691c55aabb3c4e3b89b953dd2f022f7ea845e5443954767d321d5f5fa394e28c
Status: Downloaded newer image for mysql:latest
```

```sh
$ docker run --rm --name mysql01 -e MYSQL_ROOT_PASSWORD=Password1234 -d mysql:5.6
```

```sh
$ docker run --rm --name wordpress01 --link mysql01 -p 8080:80 -e WORDPRESS_DB_HOST=mysql01:3306 -e WORDPRESS_DB_PASSWORD=Password1234 -d wordpress:4.8-apache
```