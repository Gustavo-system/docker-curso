# Docker

## Modulo Uno - Instalación

## Modulo Dos - Comandos basicos

Obtener ayuda de un comando

```
docker <comando> --help
```

---

## Imagenes

---

Descargar una imagen del repositorio de DockerHub

```
docker pull IMAGE_NAME
docker pull IMAGE_NAME:TAG

ejemplo:
- docker pull postgres
- docker pull postgres:15.1
```

Mostrar todas las imagenes descargadas

```
docker images

docker image ls
```

Borrar una o mas imagenes

```
docker image rm <image-ID> o <ID1 ID2 ID3…>

docker rmi name_imagen
```

Borrar todas las imagenes sin importar nada

```
docker image prune
```

Borrar todas las imágenes no usadas

```
docker image prune -a
```

---

## Contenedores

---

Mostrar un listado de todos los contenedores corriendo en el equipo

```
docker container ls

docker ps
```

Mostar todos los contenedores que esten corriendo o no esten corriendo
```
docker container ls -a
```

Visualizar solo las ids de los contenedores
```
docker container ls -aq

docker ps -aq
```

Comprobar el espacio utilizado por los contenedores creados
```
docker container ls -a -s

docker ps -a -s
```

Ver los dos últimos contenedores lanzados
```
docker container ls -a -n <numero>

docker ps -a -n <numero>
```

Si necesitamos buscar un contenedor por algun dato en concreto
- \-f = lo usamos si necesitamos filtrar un contenedor por alguna palabra clave
```
docker ps -a -f name=<nombre>
```

Correr un contenedor:

-   \-d = Corre la imagen desenlazada de la consola donde se ejecutó el comando
-   \-p = Especificar un puerto, (puerto de la computadora : puerto del contenedor)
-   imagen-name = nombre de la imagen a usar
-   \-e = variable de entorno (-e variable = valor)
-   \-v = volumen que se usara para el contenedor (-v volumen = ruta-contendor)
-   \--network = network que se usara para comunciarce entre dos o mas contenedores ( -n network-name)

```
docker container run -d -p 80:80 <image-name>
```

-   ejemplo:

```
docker container run \
-d \
-p <puerto-fisico>:<puerto-container> \
--network <network-name> --network-alias <network-alias> \
-v <volumen-name>:<ruta-container> \
-e <variable>=<valor-variable> \
<imagen>:<tag>
```

Iniciar un contenedor previamente creado

```
docker container start <container-id>
```

Asignar un nombre al contenedor

-   \--name = se asignara el nombre que se desee al contenedor, remplasando al default

```
docker container run --name myName IMAGE_NAME
```

Detener un contenedor

```
docker container stop <container-id>
```

Detener contenedores y removerlos de forma forzada

-   \-f = hará que se elimine el contenedor o los contenedores aunque este corriendo

```
docker container rm -f <container-id> o <ID1 ID2…>
```

Borrar contenedores

```
docker container rm <container-id>
```

Borrar todos los contenedores detenidos

```
docker container prune
```

Ver los logs del contenedor

-   \--follow = dará seguimiento a todos los logs en tiempo real segun se menupule el contenedor

```
docker container logs <container-id>

docker container logs --follow CONTAINER
```

<br>

## Modulo Tres - Persistencia de datos

---

## Volumes

---

### Hay 3 tipos de volúmenes, son usados para hacer persistente la data entre reinicios y levantamientos de imágenes.

#### - Named Volumes

Crear un volumen

```
docker volume create <coustom-name>
```

Listar los volúmenes creados

```
docker volume ls
```

Inspeccionar el volumen específico

```
docker volume inspect <valumen-name>
```

Remueve todos los volúmenes no usados

```
docker volume prune
```

Remueve uno o más volúmenes especificados

```
docker volume rm <valumen-name>
```

Usar un volumen al correr un contenedor

```
docker run -v <volumen-name>:<ruta-contenedor> <imagen>
```

#### - Bind volumes - Vincular volúmenes

Bind volumes trabaja con paths absolutos Terminal

```
docker run \
-d \
-p <puerto>:<puerto-contendor> \
-w <directorio> \
-v "$(pwd):<directorio>" \
<imagen>:<tag>
sh -c <comando-consola>
```

-   ejemplo:

```
docker run \
-d \
-p 3000:3000 \
-w /app \
-v "$(pwd):/app" \
node:18-alpine \
sh -c "yarm install && yarn run dev"
```

#### - Anonymous Volumes

Volúmenes donde sólo se especifica el path del contenedor y Docker lo asigna automáticamente en el host

```
docker run -v <ruta-contenedor>
```

---

## Container Networking

---

Si dos o más contenedores están en la misma red, podrán hablar entre sí. Si no lo están, no podrán.

Crear una nueva red

```
docker network create <coustom-name>
```

Listar todas las redes creadas

```
docker network ls
```

Inspeccionar una red

```
docker network inspect <network-name o network-id>
```

Borrar todas las redes no usadas

```
docker network prune
```

Borrar una o mas redes

```
docker network rm <network-id o ids>
```

Conectar un contenedor a una red

```
docker network connect <network-id> <container-id>
```

---

## Terminal interactiva

---

Iniciar una terminal interactiva dentro del contenedor
```
docker exec -it <container-id> <ruta-terminal>

ejemplo:
docker exec -it 111 /bin/sh
```

Salir de la terminal interactiva
```
exit
```
<br>

[Repaso de lo aprendido :)](https://gist.github.com/Klerith/8cfc637868212cfb888333ecaa6080e1)

<br>

## Modulo Cuatro - Docker Compose

---

## Multipes contenedores

---


Ejecutamos la configuracion creada en el docker-compose
```
docker compose up
```

En caso de que no se cree de forma correcta todo lo del docker-compose se puede hacer una limpieza
```
docker compose down
```

- Todos los contenedores creados en el docker-compose se agregan a la misma red aunque no se especifique

Ejemplo de docker-compose

```
version: "3"

services:
  # nombre del servicio
  db:
    # nombre del contenedor
    container_name: posgrest_db
    # definimos la imagenes que se usara
    image: postgres:15.1
    # definimos el volumen que usara este contenedor
    volumes:
      - postgres-db:/var/lib/postgresql/data
    # se declaran las variables de entrono
    environment:
      - POSTGRES_PASSWORD=123456

  pgAdmin:
    # depende de otro servicio, importa el orden de como se estrucutura el yml
    depends_on:
      - db
    container_name: pgAdmin
    image: dpage/pgadmin4:6.17
    environment:
      - PGADMIN_DEFAULT_PASSWORD=123456
      - PGADMIN_DEFAULT_EMAIL=superman@google.com
    # se definen los puertos que se usaran
    ports:
      # puerto maquina : puerto contenedor
      - "8080:80"

# se requiere deifinir el volumen que se usara en el contenedor, para saber si es uno existente o uno nuevo. En este caso es externo
volumes:
  # nombre del volumen
  postgres-db:
    # define que se use un volumen externo
    external: true
```

---

## Variables de entorno

---

Se utilizan para no exponer todos los datos privados cuando se suba al contenedor o al repositorio

1. Crear un archivo .env

- Ejemplo de archivo .env
```
ENV_NAME_CONTAINER = postgres-db
ENV_NAME_USER = postgres
ENV_PASSWORD = 123456
```

- Se invocan en el archivo "docker_componse.yml"
	- Se coloca dento de la siguente sintaxis = ${env}
	
```
version: "3"

services:
  db:
    container_name: ${ENV_NAME_CONTAINER}
    image: postgres:15.1
    volumes:
      - postgres-db:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=${ENV_PASSWORD}
	  - POSTGRES_USER=${ENV_NAME_USER}
```

<br>
<br>

## Glosario de Términos

-   Docker
    -   Docker es una herramienta diseñada para facilitar la creación, la implementación y ejecucion de aplicaciones mediante el uso de contenedores.
-   Container - Contenedor
    -   Es una instancia de una imagen ejecutándose en un ambiente aislado.
-   Image - Imagen de contenedor
    -   Es un archivo construido por capas, que contiene todas las dependencias para ejecutarse, tal como:
        -   las dependencias
        -   configuraciones
        -   scripts
        -   archivos binarios
