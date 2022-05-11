# Instalación de Traccar con Mysql y Phpmyadmin
## Modo de instalación

Antes de ejecutar el docker-compouse debemos de modificar a nuestras necesidades las variables ubicadas en el archivo docker-compose.yml y conf/traccar.xml.

 * Primero debemos de configurar las siguientes lineas en el archivo docker-compose.yml:
```
version: '3'
 
services:
  db:
    image: mysql:5.7
    container_name: db
    environment:
      MYSQL_ROOT_PASSWORD: root-password  --->  Ingresar el password del usuario root de la base de datos
      MYSQL_DATABASE: traccar --------------->  Ingresar en nombre de la base de datos, para este ejemplo dejamos traccar.
      MYSQL_USER: user ---------------------->  Ingresar el usuario que será utilizado para la base de datos traccar.
      MYSQL_PASSWORD: password -------------->  Ingresar la contraseña para el usuario traccar.
    ports:
      - "6033:3306"
    volumes:
      - ./dbdata:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: pma
    links:
      - db
    environment:
      PMA_HOST: db
      PMA_PORT: 3306
      PMA_ARBITRARY: 1
    restart: always
    ports:
      - 8081:80

  traccar:
    image: traccar/traccar
    hostname: traccar
    restart: always
    ports:
      - "5000-5150:5000-5150"
      - "8082:8082"
    volumes:
      - traccar-db:/opt/traccar/data/database
      - ./conf/traccar.xml:/opt/traccar/conf/traccar.xml

volumes:
  traccar-db:
  dbdata:
```

 * Segundo debemos de modificar la base, nombre de usuario y contraseña que hemos definido en la primera parte en el archivo conf/traccar.xml

 * Tercero ejecutar el comando
```
docker-compose up -d --build
```

 * ¡¡¡¡Esperar unos segundo a que se cree las los contenedores y listo!!!!
