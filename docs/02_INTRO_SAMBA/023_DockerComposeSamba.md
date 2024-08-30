--- 
title: Docker Compose Samba
description: Introducción a la herramienta Docker compose con una imagen de Samba por Francisco Javier Hernández Illán. Práctica para aplicar un docker compose de Samba. 
---

# Docker Compose Samba

En la **UT1** se realizó la instalación de docker y creación de contenedores de la imagen de **NextCloud**. En los últimos comandos de las prácticas realizadas, se necesitaban una gran cantidad de argumentos para la configuración del contenedor en una red y aplicar un volumen al mismo. Este hecho repercutía en una sintaxis demasiado extensa de dichos comandos.

Para una gestión mejorada de los comandos extensos de `docker run` destaca una herramienta de Docker denominada **Docker-Compose**, la cual ayuda a definir, ejecutar y conectar aplicaciones de múltiples contenedores, mediante un archivo **YAML**.

En esta sección se detalla esta herramienta de docker para aplicarla a una imagen de **Samba**.

## Docker-compose

!!! Summary "Flujo de trabajo"
    Contenedores→ docker run → docker_compose.yml

* **Docker-compose** es otro proyecto open source que permite definir aplicaciones multi-contenedor de una manera sencilla y declarativa.

* Es una alternativa más cómoda al uso de los comandos docker run, que resultan un tanto tediosos cuando trabajamos con aplicaciones de varios componentes.

* Se define un fichero `docker-compose.yml` que se puede observar en el ejemplo realizado en la práctica en el último apartado.

!!! tip "Propina"
    Ficheros [YAML](https://es.wikipedia.org/wiki/YAML)

!!! Example
    A continuación se muestra un ejemplo para la realización de la práctica de ampliación: [Instalación Wordpress](https://github.com/docker/awesome-compose/tree/master/official-documentation-samples/wordpress/)

1. **Primer Paso**: instalación de Docker-compose.

* [Install Docker Compose](https://docs.docker.com/compose/install/)

```bash
apt-get install docker-compose
```

2. **Segundo paso**: Crear directorio del proyecto y dentro el archivo `yaml`.

```bash
mkdir -p $HOME/my_wordpress && \
cd $HOME/my_wordpress
```

3. **Tercer paso**: se crea y edita el `yaml`.

``` yaml
cat << EOF > $HOME/my_wordpress/docker-compose.yml
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
EOF
```

4. **Cuarto paso**: en la carpeta creada del proyecto se ejecuta el comando de `docker-compose up -d`

``` bash
docker-compose up -d
```

!!! tip "Comprobación"
    Se comprueba que se ha creado el contenedor y se abre el navegador para comprobar que está disponible la instalación de wordpress.

## PT22. Samba mediante Docker compose

!!! info "Contribución a los CEs (Criterios de Evaluación)"
    Estas actividades contribuyen a los criterios de evaluación CE61, CE62, CE63, CE64, CE65, CE9 del RA6 de SOR.

!!! Abstract "Situación de Aprendizaje"
    En la instalación de la carpeta compartida en el **IES Severo Ochoa d'Elx** de la **PT21**, uno de los profesores encargados de la revisión de la instalación solicita si es posible realizar la instalación de una forma más rápida y sencilla, debido a los conocimientos en docker decides utilizar la herramienta **Docker-Compose**.

!!! question "Tarea"
    A partir del `docker-compose` adjunto realiza un video con los pasos a realizar a continuación.

### Desde el servidor

- Crea un contenedor con el servicio de Samba mediante la guía adjunta.
- Comprueba que el contenedor corriendo.

Se va a realizar unos pasos previos para preparar el entorno. En primer lugar creamos las carpetas donde alojar el proyecto:

``` bash
mkdir -p $HOME/docker/samba/compartido && \
cd $HOME/docker/samba
```

A continuación se da permisos de escritura y lectura a la carpeta compartida:

``` bash
chmod 777 -R $HOME/docker/samba/compartido
```

Ahora se crea el fichero de configuración `docker-compose.yml` mediante la [imagen de docker](https://hub.docker.com/r/dperson/samba) lanzando el siguiente comando:

``` yaml
cat << EOF > $HOME/docker/samba/docker-compose.yml
version: '3.4'

services:
  samba:
    container_name: samba
    image: dperson/samba:latest
    environment:
      TZ: 'Europe/Brussels'
    networks:
      - default
    ports:
      - "137:137/udp"
      - "138:138/udp"
      - "139:139/tcp"
      - "445:445/tcp"
    read_only: true
    tmpfs:
      - /tmp
    restart: always
    stdin_open: true
    tty: true
    volumes:
      - $HOME/docker/samba/compartido:/sordata:z
    command: '-s "DockerSambaCompartida;/sordata" -n'

networks:
  default:
EOF
```

!!! tip "Aclaraciones"
    - La línea de `volumes` monta la carpeta creada inicialmente en el `/home` en la carpeta sordata en el contenedor.
    - [La opción z](https://stackoverflow.com/questions/35218194/what-is-z-flag-in-docker-containers-volumes-from-option) le dice a Docker que el contenido del volumen se compartirá entre contenedores. 

Principales parámetros a modificar para adaptarlos a nuestro sistema y configuración especifica:

|Parámetro|Función|
| - | - |
|~/docker/samba/compartido:/sordata|Definimos ruta donde alojamos los ficheros compartidos|
|137:137|Puerto protocolo NetBios|
|138:138|Puerto protocolo NetBios|
|139:139|Puerto protocolo SMB|
|445:445|Puerto protocolo SMB|
|restart: always|Habilitamos que tras reiniciar la maquina anfitrión vuelva a cargar el servicio|

Una vez configurado, se levanta el contenedor para ser creado y ejecutado mediante compose:

``` bash
docker-compose up -d
```

### Desde el cliente

!!! question "Tarea"
    **Explica en el video los siguientes apartados:**

- Accede a la carpeta compartida en un cliente, puede ser tu propio Host.
- Crea un archivo de texto de prueba desde el servidor, entra al contenedor, servidor y cliente para confirmar que se ha creado.
- ¿Qué pasa si paras y destruyes el contenedor? ¿Por qué?.

<!-- !!! tip "Consejo"
    Montar la carpeta desde el raíz del servidor a cualquier carpeta. -->

<!-- ¿Por qué se puede montar la carpeta desde la raíz del servidor? Te puedes apoyar en las notas de la imagen de NFS para contestar. Imagen en el siguiente enlace: [itsthenetwork/nfs-server-alpine](https://hub.docker.com/r/itsthenetwork/nfs-server-alpine) -->

