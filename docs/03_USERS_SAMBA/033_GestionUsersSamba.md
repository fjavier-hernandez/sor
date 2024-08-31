--- 
title: Gestión de Usuarios y grupos en Samba
description: Gestión de Usuarios y grupos en Samba de Francisco Javier Hernández Illán. 
---

# Gestión de secciones y usuarios en Samba

## PT32 Gestión de secciones y usuarios en Samba 

!!! info "Contribución a los CEs (Criterios de Evaluación)"
    Estas actividades contribuyen a los criterios de evaluación CE6, y CE9 del RA2 de SOR.

!!! warning "Advertencia"
    Razona todas las respuestas, en caso de que la respuesta sea debido a unos permisos indica que permisos tiene y donde han sido determinados.

!!! question "Tareas"
    - A partir de la **PT31** los siguientes apartados:
    - Adjunta un vídeo con los pasos realizados y un informe.

### Secciones SAMBA

!!! note "**NOTA:**"
    - Recuerda reiniciar el servicio de samba tras modificar el fichero smb.conf.
    - Estate atento para eliminar las líneas comentadas, las cuales comienzan por `“#”` y `“;”`

1. Haz que todos los `/homes` de los usuarios sean navegables, que cualquiera pueda acceder sin credenciales y que solo tengan permisos de lectura. Recuerda que también deberás tener en cuenta los permisos dentro del servidor Linux. Haz captura de la configuración realizada en `smb.conf` y del explorador de Windows accediendo a tu `/home` de Ubuntu.
2. Cambia en el servidor, en la sección [homes], la opción read only a no. Haz una captura de los permisos de tu carpeta `/home`. Prueba a crear un fichero desde Windows. ¿Qué sucede y por qué?
3. Indica la configuración del servicio más adecuada si quisiera permitir únicamente los equipos de la red 172.16.0.0/16 (Supuesta IP de las máquinas de la organización, configurada en la RED NAT por defecto). ¿Sigues teniendo acceso desde el cliente? ¿Por qué?
4. Ahora genera secciones con las carpetas generadas para **BerbeWild S.A.** y realiza los permisos necesarios para que solo accedan los usuarios de los grupos correspondientes.

### Usuarios SAMBA

5. Prueba a crear un usuario con `smbpasswd` sin que éste exista en el sistema Linux. ¿Qué sucede? Adjunta una captura.
6. Crea el usuario **leonardo** en el grupo de TIC en el sistema linux, ten en cuenta que dicho usuario solo debe ser capaz de acceder al sistema a través de **Samba**. Posteriormente créalo para **Samba**. Visualiza los usuarios de samba y fíjate en el `UID` asignado al usuario recién creado ¿tiene el mismo UID que en el sistema Linux (`/etc/passwd`)? Adjunta capturas de todo el proceso.
7. Cierra la sesión actual en el servidor (exit) e intenta iniciar sesión con el usuario “leonardo” ¿Qué sucede y por qué?
8. Edita en el servidor la sección [homes] con “force user = root”. Reinicia el servicio. Intenta crear, desde el cliente de Windows, "logueandote" como **leonardo** un documento en el escritorio del `/home` compartido. ¿Te permite crearlo? ¿Por qué? ¿Qué permisos y usuario propietario tiene el fichero creado? Adjunta capturas a las explicaciones.
9. Cambia la configuración en el servidor de la carpeta “`/home/leonardo/serviciotecnico`” para que no permita el acceso de invitados y solo permita al usuario **“leonardo”**. Muestra la configuración. Intenta acceder desde el cliente Windows con las credenciales de tu usuario ¿Qué sucede? Adjunta capturas de pantalla.
10. Comprueba el acceso y no acceso de los usuarios de **BerbeWild S.A.** a los diferentes directorios creados.