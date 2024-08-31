--- 
title: Definición Práctica NextCloud
description: Práctica guiada de instalación de NextCloud mediante Docker Docker por Francisco Javier Hernández Illán. Gestión de recursos compartidos en NextCloud utilizando Volúmenes persistentes. 
---

<figure style="float: right;">
    <img src="imagenes/NextCloudLogo.png" width="200"/>
</figure>

## NextCloud

**Nextcloud** hace referencia al conjunto de herramientas software de **código abierto** con arquitectura **cliente-servidor**, que permiten la creación de **servicios de alojamiento de archivos** en servidores privados.

### Características

- Los archivos Nextcloud **son almacenados en estructuras de directorio convencionales** y se pueden acceder a través del protocolo **WebDAV** si es necesario.
- Los archivos son **encriptados** en la transmisión y opcionalmente durante el almacenamiento.
- Los usuarios pueden manejar **calendarios** (CalDAV), **contactos** (CardDAV), **tareas programadas** y reproducir **contenido multimedia** (Ampache).
- Permite la administración de usuarios y grupos de usuarios (vía OpenID o LDAP) y definir permisos de acceso.
- Posibilidad de añadir aplicaciones (de un solo clic) y conexiones con **Dropbox**, **Google Drive** y **Amazon S3**.
- Disponibilidad de acceso a diferentes **bases de datos** mediante SQLite, MariaDB, MySQL, Oracle Database, y PostgreSQL.

!!! note "Nota"
    **WebDAV** es un grupo de trabajo del **Internet Engineering Task Force (IETF)**. El término significa **"Autoría y versionado distribuidos por Web" (Web Distributed Authoring and Versioning)**, y **se refiere al protocolo** (más precisamente, a la extensión del protocolo) que el grupo definió. Este protocolo proporciona funcionalidades para **crear, cambiar y mover documentos en un servidor remoto** (típicamente un servidor web).

## PT11. Instalación y configuración de NextCloud mediante Docker

!!! info "**Contribución a los CEs (Criterios de Evaluación)**"
    Las actividades de esta práctica contribuyen a los criterios de evaluación del **RA1: CE1, CE2, CE6, CE8 y CE9**, del **RA4: CE2 y CE5**, y del **RA6: CE1, CE2, CE3, CE4, CE5, CE6 y CE9** de SOR.

!!! Abstract "**Situación de Aprendizaje**"
    - Siguiendo la **situación de aprendizaje**, estamos trabajando en el departamento TIC de **NetOS** y nos encargan un proyecto para instalar de una empresa líder en el sector hotelero un servicio que cubra la necesidad de compartir recursos (carpetas documentos, imágenes...), tanto entre las personas dentro de la empresa como con clientes fuera de ésta.
    - Para hacernos valer dentro de la empresa indicamos que podemos instalar un servidor **NextCloud** en unos pocos minutos para solventar dicho problema.
    - La empresa cuenta con un servidor con **Ubuntu 22.04** para instalar el servicio, por lo tanto decidimos hacer uso de **docker** para levantar el servicio **NextCloud** de una forma muy rápida y sencilla.

!!! tip "**Premisas**"
    - El servicio docker se debe levantar en la red **192.168.20.0/24**.
    - Debe tener la traducción estática del puerto **80** de la máquina anfitriona al puerto **80** del contenedor.
    - Debe tener un volumen asociado llamado **"volNextCloud"**.

!!! question "**Tareas**"
    - **Actividad P11_1**: enlace al video de la instalación de Docker (3 puntos al RA1).
    - **Actividad P11_2**: enlace al video de la instalación de NextCloud (3 puntos al RA6).
    - **Actividad P11_3**: enlace al video de la gestión de recursos compartidos mediante volúmenes de Docker (3 puntos al RA4).
    - **Actividad P11_4**: entrega de pdf con la resolución de las preguntas de Comprensión descritas en el siguiente subapartado. (3 puntos al RA4).

!!! note "Nota"
    Excepción de instrumento de calificación, al dividir la práctica en apartados se calificará cada uno de ellos con **IC1**.

### Comprensión

!!! warning "advertencia"
    Preguntas a responder en la actividad **PT11_4**:

- Crea el contenedor Nextcloud ¿Al crear el contenedor se ha generado automáticamente algún volumen? ¿Cuál?
- Dentro del sistema anfitrión (lliurex/Windows) ¿Dónde está guardado el fichero subido previamente?
- ¿Este contenedor es persistente? ¿Qué significa que el contenedor es persistente? ¿Cómo lo has comprobado?

### Ayuda

- Página oficial para la Instalación en ubuntu en el siguiente enlace [Instalación en Ubuntu Documentación](https://docs.docker.com/engine/install/ubuntu/)
- Lee la documentación sobre la imagen NextCloud que hay en [Dockerhub Nextcloud_Docker](https://hub.docker.com/_/nextcloud).

### Guía

En este enlace puedes acceder a una guía para realizar la práctica: [GUÍA INSTALACIÓN NEXTCLOUD CON DOCKER](NextCloudDockerSolucion.md)

