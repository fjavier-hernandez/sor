# HERRAMIENTAS DOCKER

Existen dos opciones destacables en Docker para agilizar el despliegue de Imágenes y contenedores: Dockerfile y Docker-compose

* **Dockerfile**: es un archivo de texto plano que contiene una serie de instrucciones necesarias para crear una imagen que, posteriormente, se convertirá en una sola aplicación utilizada para un determinado propósito.

!!! Summary
    Imágenes→ docker build → docker_file

* **Docker-compose**: es una herramienta que permite simplificar el uso de Docker. A partir de archivos `yaml` es mas sencillo crear contenedores, conectarlos, habilitar puertos, volúmenes, etc.

!!! Summary
    Contenedores→ docker run → docker_compose.yml

### Dockerfile

Las instrucciones principales que pueden utilizarse en un Dockerfile son:

<figure>
    <img src="imagenes/01/InstruccionesDockefile.png" width="850"/>
</figure>

* Ver también: [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

!!! Example
    A continuación se muestra un ejemplo para la realización de la práctica de ampliación extraido de [Apache oficial de docker](https://hub.docker.com/_/httpd).

1. **Primer paso**: generar imagen con un **Dockerfile**.

* Se crea el archivo de texto dockerfile.
```bash
touch dockerfile
nano dockerfile
```

* Ejemplo edición Dockerfile.

``` bash
# syntax=docker/dockerfile:1
FROM nginx:alpine
RUN apk update
COPY index.html/ /usr/share/nginx/html/
```

!!! Nota
    Se puede probar a introducir el volumen en el docker file: `VOLUME /myvol1`

* Se crea la imagen y el contenedor.
``` bash
docker build -t fcojavierhernandez/my-nginx-alpine .
docker run -dit --name my-nginx-alpine -p 8081:80 fcojavierhernandez/my-nginx-alpine
```

!!! Warning
    * **Mucho cuidado** con el último punto de la instucción de build, tiene un espacio antes. Es el último argumento (y el único imprescindible) es el nombre del archivo Dockerfile que tiene que utilizar para generar la imagen. Como en este caso se encuentra en el mismo directorio y tiene el nombre predeterminado Dockerfile, se puede escribir simplemente punto (`.`).
    * Para indicar el nombre de la imagen se debe añadir la opción `-t`. El nombre de la imagen debe seguir el patrón `nombre-de-usuario/nombre-de-imagen`. Si la imagen sólo se va a utilizar localmente, el nombre de usuario y de la imagen pueden ser cualquier palabra.

2. **Segundo paso**: subir imagen al Docker Hub.

* Se debe generar una cuenta de Docker Hub con la cuenta corporativa de Office 365: [Sign Up](https://hub.docker.com/signup)
* Con el comando `docker login`, se realiza el acceso a la plataforma desde el terminal, introduciendo usuario y contraseña.
``` bash
docker login
```

!!! warning "Advertencia"
    Se debe acceder con el nic no con la cuenta de correo.

* Con el comando `docker push`, se realiza el "Upload" de la imagen.

``` bash
docker push fcojavierhernandez/my-nginx-alpine
```

* Por último Se debe comprobar accediendo a la plataforma de Docker Hub.

<figure>
    <img src="imagenes/01/DockerHub.png"/>
</figure>


### Docker-compose

* **Docker-compose** es otro proyecto open source que permite definir aplicaciones muilti-contenedor de una manera sencilla y declarativa.

* Es una alternativa más cómoda al uso de los comandos docker run y docker build, que resultan un tanto tediosos cuando trabajamos con aplicaciones de varios componentes.

* Se define un fichero `docker-compose.yml` que se puede observar en el ejemplo realizado en la práctica en el último apartado.

!!! Example
    A continuación se muestra un ejemplo para la realización de la práctica de ampliación: [Instalación Wordpress](https://docs.docker.com/samples/wordpress/)

1. **Primer Paso**: instalación de Docker-compose.

* [Install Docker Compose](https://docs.docker.com/compose/install/)

```bash
apt-get install docker-compose
```

2. **Segundo paso**: Crear directorio del proyecto y dentro el archivo `yaml`.

```bash
mkdir my_wordpress
touch docker-compose.yml
nano docker-compose.yml
```

3. **Tercer paso**: se edita el `yaml`.

``` yaml
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

4. **Cuarto paso**: en la carpeta creada del proyecto se ejecuta el comando de `docker-compose up -d`

``` bash
docker-compose up -d
```

!!! tip "Comprobación"
    Se comprueba que se ha creado el contenedor y se abre el navegador para comprobar que está disponible la instalación de wordpress.

## Actividades tipo Prácticas/trabajos (AP)

### PT64. NFS mediante Docker compose

A partir del `docker-compose` adjunto realiza un informe con los siguientes apartados:

- Crea un contenedor con el servicio de NFS. Adjunta captura con el contenedor corriendo.
- Monta la carpeta compartida de forma automática en un cliente. Adjunta capturas de los pasos a realizar.

!!! tip "Consejo"
    Montar la carpeta desde el raíz del servidor a cualquier carpeta.

¿Por qué se puede montar la carpeta desde la raíz del servidor? Te puedes apoyar en las notas de la imagen de NFS para contestar. Imagen en el siguiente enlace: [itsthenetwork/nfs-server-alpine](https://hub.docker.com/r/itsthenetwork/nfs-server-alpine)

- Crea un archivo de texto de prueba, entra al contenedor o servidor y confirma que se ha creado.
- ¿Qué pasa si paras y destruyes el contenedor? ¿Cómo se podría solucionar?.

- NFS docker-compose:

``` yaml
version: "2.1"
services:
  # https://hub.docker.com/r/itsthenetwork/nfs-server-alpine
  nfs:
    image: itsthenetwork/nfs-server-alpine:12
    container_name: nfs
    restart: unless-stopped
    privileged: true
    environment:
      - SHARED_DIRECTORY=/data
    volumes:
      - /data/nfs-storage:/data
    ports:
      - 2049:2049
```

## Soluciones

[Solución Práctica NFS-DockerCompose](NFSDockerComposeSolucion.md)