# Docker

Documentación sobre Docker.

  

Usar el repositorio Dockerhub

[https://hub.docker.com/](https://hub.docker.com/)

  

**Comando básicos**

  

Remover todas las configuraciones.

```plain
docker system prune -f
```

Lista los contenedores que están corriendo.

```plain
docker ps
```

  

Revisar el logs del contenedor

```plain
docker logs <nombre/container id> -f
```

  

Ejecuta un comando dentro de un contenedor que está corriendo. it = sección interactiva con terminal

```plain
docker exec -it <container id> sh
```

  

Iniciar y detener el contenedor

```plain
docker start/stop <container id>
```

  

Ejecutar una versión específica de la imagen y pasar variables de configuración <repositorio>:<tag>

```plain
docker run -e POSTGRES_PASSWORD=Datapar.2022 postgres:9.6
```

Ejecutar en background el contenedor

```plain
docker run -d nginx
```

  

Listar las interfaces creadas

```plain
 docker network ls
```

  

Ver los IPs asignados a los contenedores

```plain
docker ps -q | xargs -n 1 docker inspect --format '{{ .Name }} {{range .NetworkSettings.Networks}} {{.IPAddress}}{{end}}' | sed 's#^/##';
```

  

**Trabajando con Dockerfiles**

  

Construir un contenedor a través del archivo

```plain
docker build -t ibanez-node .
```

  

\-d = ejecutar en segundo plano

\-v = montar la carpeta y mantener los datos

\-p = para hacer visible el contendedor puerto\_local:Puerto\_contenedor

```plain
docker run -d -v /root/app/etc:/etc/todos -p 3000:3000 ibanez-node
```

  

Una vez terminado de realizar tus cambios, debemos de agregar un tag para reconstruir una nueva imagen, ya que sufrió cambios.

```plain
docker build -t ibanez-node:v2 .
```

  

  

**Levantar tus contenedores a docker hub**

Ingresamos a nuestra cuenta de docker hub y luego subimos la imagen

  

```plain
root@ti:~/app# docker login 
```

  

![](https://t3092159.p.clickup-attachments.com/t3092159/61200baf-a7b2-4d96-bf92-a89c1ca3dd17/image.png)

  

Renombramos nuestra imagen para mantener un buen formato (buenas prácticas).

```plain
docker tag 29c3815e9739 ibanezpy/ibanez-node:v2
```

Comando para subir la imagen a tu repositorio

```plain
docker push ibanezpy/ibanez-node:v2
```

![](https://t3092159.p.clickup-attachments.com/t3092159/65923aa0-2cb5-4718-85b4-51272c29c1fe/image.png)

  

Ingresamos a nuestra cuenta y vemos que ya levanto el contenedor.

![](https://t3092159.p.clickup-attachments.com/t3092159/625bf7a5-b904-4fbb-9c97-f35c3387c816/image.png)

  

**Multicontenedores**

  

Creamos la red para nuestros contenedores

```plain
docker network create todo-app
```

  

Creamos el contenedor para la base de datos

```plain
docker run -d \
> --network todo-app --network-alias mysql \
> -v todo-mysql-data:/var/lib/mysql \
> -e MYSQL_ROOT_PASSWORD=Datapar.2022 \
> -e MYSQL_DATABASE=todos \
> mysql:5.7
```

  

Ahora conectamos nuestra aplicación a la base de datos creada recientemente

```plain
docker run -dp 3000:3000 \
 --network todo-app \
 -e MYSQL_HOST=mysql \
 -e MYSQL_USER=root \
 -e MYSQL_PASSWORD=Datapar.2022 \
 -e MYSQL_DB=todos \
 ibanez-node:v2
```

  

  

**Utilizando docker**

  

Ejemplo del archivo docker-compose.yaml

```plain
version: "3.7"

services:

#docker run -dp 3000:3000 --network todo-app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos getting-started:v2

  app:
    image: pablokbs/getting-started:v2
    ports:
      - 3000:3000
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

# docker run -d     --network todo-app --network-alias mysql     -v todo-mysql-data:/var/lib/mysql     -e MYSQL_ROOT_PASSWORD=secret     -e MYSQL_DATABASE=todos     mysql:5.7

  mysql:
    image: mysql:5.7
    volumes:
      - ./todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos
```

  

Ejecutar el Docker compuse del archivo docker-compose.yaml

```plain
docker-compose up -d
```

  

Bajar todos los contendedores

```plain
docker-compose down
```