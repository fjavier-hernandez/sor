--- 
title: Refuerzo y Ampliación UT2 Introducción Samba
description: Prácticas de refuerzo y ampliación por Francisco Javier Hernández Illán. Introducción Samba. 
---

# Refuerzo y Ampliación

A continuación se definen las actividades de refuerzo y ampliación de la UT2

## Refuerzo

### PT23. Generar carpeta compartida con acceso total

!!! info "Contribución a los CEs (Criterios de Evaluación)"
    Estas actividades contribuyen a los criterios de evaluación CE5, CE7 y CE9 del RA1 de SOR.

!!! question "Tareas"
    A partir de la guía adjunta en la práctica PT21 realiza una carpeta compartida en el servicio de samba que tenga permisos totales desde cualquier usuario, haz pruebas. Realiza un vídeo con los pasos realizados.

## Ampliación

### PT24. Generar carpeta con permisos desde Docker Compose Samba.

!!! info "Contribución a los CEs (Criterios de Evaluación)"
    Estas actividades contribuyen a los criterios de evaluación CE61, CE62, CE63, CE64, CE65, CE9 del RA6 de SOR.

!!! Abstract "**Situación de Aprendizaje**"
    - Siguiendo la **situación de aprendizaje**, de PT22, ahora la necesidad es crear una carpeta accesible por cualquier usuario din necesidad de autentificación, y otra por el usuario Robert cuyas características son: navegable y con autentificación

!!! question "Tareas"
    Realiza un vídeo con los pasos para configurar las carpetas que se indican en la situación de aprendizaje mediante **Docker compose**.

!!! tip "Ayuda"
    Para conseguir la configuración de las carpetas puedes investigar el siguiente archivo `yaml` de docker compose [Docker Compose dperson](https://github.com/dperson/samba/blob/master/docker-compose.yml), con la siguiente explicación del archivo en el foro de Docker [Foro Docker Compose dperson](https://forums.docker.com/t/help-needed-setting-up-samba-dperson/112829)