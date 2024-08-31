--- 
title: Refuerzo y Ampliación UT1 Introducción SOR
description: Prácticas de refuerzo y ampliación por Francisco Javier Hernández Illán. Gestión de recursos compartidos en NextCloud utilizando Volúmenes persistentes. 
---

# Refuerzo y Ampliación

A continuación se definen las actividades de refuerzo y ampliación de la UT1

## Refuerzo

### PT12. Instalación y configuración de OwnCloud mediante Docker

!!! info "**Contribución a los CEs (Criterios de Evaluación)**"
    Las actividades de esta práctica contribuyen a los criterios de evaluación del **RA1: CE1, CE2, CE6, CE8 y CE9**, del **RA4: CE2 y CE5**, y del **RA6: CE1, CE2, CE3, CE4, CE5, CE6 y CE9** de SOR.

!!! Abstract "**Situación de Aprendizaje**"
    - Siguiendo la **situación de aprendizaje**, estamos trabajando en el departamento TIC de NetOS y nos encargan un proyecto para instalar de una empresa líder en el sector del turismo un servicio que cubra la necesidad de compartir recursos (carpetas documentos, imágenes...), tanto entre las personas dentro de la empresa como con clientes fuera de ésta.
    - Para hacernos valer dentro de la empresa indicamos que podemos instalar un servidor **OwnCloud** en unos pocos minutos para solventar dicho problema.
    - La empresa cuenta con un servidor con **Ubuntu 22.04** para instalar el servicio, por lo tanto decidimos hacer uso de **docker** para levantar el servicio **OwnCloud** de una forma muy rápida y sencilla.

!!! tip "**Enunciado**"
    - Realiza la instalación de Docker en un **Ubuntu 22.04**, posteriormente realiza la instalación de OwnCloud mediante su imagen de Docker oficial.
    - Genera un volumen persistente.
    - Genera un archivo de prueba en el servidor.
    - Comprueba que al realizar la eliminación del contenedor y vuelta a generar la información no se ha perdido.

!!! question "**Tareas**"
    - **Actividad PT12_1**: enlace al video de la instalación de Docker (3 puntos al RA1).
    - **Actividad PT12_2**: enlace al video de la instalación de OwnCloud (3 puntos al RA6).
    - **Actividad PT12_3**: enlace al video de la gestión de recursos compartidos mediante volúmenes de Docker (3 puntos al RA4).

!!! tip "ayuda"
    Para la instalación de OwnCloud puedes guiarte en su página oficial: [Docker OwnCloud](https://doc.owncloud.com/server/next/admin_manual/installation/docker/)

## Ampliación

### PT13. Instalación aplicaciones Cloud.

!!! info "**Contribución a los CEs (Criterios de Evaluación)**"
    Las actividades de esta práctica contribuyen a los criterios de evaluación del **RA1: CE1, CE2, CE6, CE8 y CE9**, del **RA4: CE2 y CE5**, y del **RA6: CE1, CE2, CE3, CE4, CE5, CE6 y CE9** de SOR.

!!! Abstract "**Situación de Aprendizaje**"
    - Después del éxito de la instalación de servicios "Cloud" para la compartición de recursos, tu superior o superiora solicita que hagas un estudio de las diferentes aplicaciones Cloud más utilizadas actualmente.

!!! tip "**Enunciado**"
    - Realiza un estudio de comparación de las mejora Aplicaciones Cloud para la compartición de recursos actuales.
    - Elije una APP para instalar con Docker.
    - Haz una prueba de instalación con volumen persistente.

!!! question "**Tareas**"
    - Realiza un informe de la comparativa de las APP de Cloud y un vídeo de la instalación y pruebas de los recursos compartidos con el volumen persistente en Docker. (3 puntos IE1)