# AC62. Instalación y preparación Servidor Samba

## Instalación

``` yaml
sudo apt install samba
```

## Configuración de carpetas compartidas

1. Editamos el fichero de configuración: `/etc/samba/smb.conf`

2. Al final del archivo de configuración añadiremos una nueva entrada con el **directorio a compartir**:

``` yaml
[ejercicio1]
    comment = Prueba Ejercicio 1
    path = /srv/samba/ejercicio1
    browsable = yes
    guest ok = yes
    read only = yes
    create mask = 0764
    directory mask = 0764
```

3. Creamos el directorio que íbamos a compartir y cambiamos los permisos para que no de conflicto.

``` yaml
sudo mkdir -p /srv/samba/ejercicio1
sudo chmod 764 /srv/samba/ejercicio1/
```

4. Siempre que modifiquemos el fichero de configuración debemos reiniciar el servicio:

``` yaml
sudo systemctl restart smbd
```

## Acceso desde Cliente Windows

1. Abrimos un explorador de directorios.
2. Seleccionamos Red (Network)
3. Puede que tengamos que habilitar el descubrimiento de redes:

<figure>
  <img src="./imagenes/07/072/004.png" width="650"/>
</figure>

Como alternativa, en caso de no descubrir el equipo podemos abrir un explorador de directorio y colocar en la barra de direcciones `\\IP_Servidor`

<figure>
  <img src="./imagenes/07/072/005.png" width="650"/>
</figure>

## Cuestiones sobre la práctica

Razona todas las respuestas, en caso de que la respuesta sea debido a unos permisos indica que permisos tiene y donde han sido determinados.

1. Sigue todos los pasos del manual (apartado anterior) para conseguir compartir ficheros. Adjunta una captura de la sección del fichero de configuración de samba donde se muestre la configuración del recurso compartido.
2. **Desde el servidor**, crea un fichero hola.txt en la carpeta `/srv/samba/ejercicio1`.
3. **Desde el cliente windows** accede al servidor mediante un explorador de directorios e intenta abrir el documento ¿Existe algún problema para ello? Razona tu respuesta.
4. Seguidamente modifica el fichero e intenta guardarlo. ¿Qué ocurre? ¿Por qué?
5. Ahora, desde el cliente Windows, intenta crear un fichero. ¿Te deja? ¿Puedes leer y escribir en él? ¿Por qué?
6. **Desde el servidor,** modifica los permisos de `/srv/samba/ejercicio1/hola.txt` para que tenga los siguientes permisos rwxr—rwx, ahora desde el cliente Windows prueba a modificar el contenido de dicho fichero. ¿Qué ocurre y por qué? Si no te deja escribir y guardar los cambios, indica que deberías cambiar para que puedas hacerlo.
7. Crea una carpeta en el servidor `/srv/samba/ejercicio2`. Comparte dicha carpeta, añadiendo una nueva entrada en el fichero de configuración de samba. La carpeta debe ser navegable, se debe poder acceder sin credenciales, debe tener permisos  de lectura/escritura y además los ficheros creados por los clientes podrán ser modificados, leídos y ejecutados por cualquiera. Por otro lado, los directorios creados por los clientes solo podrán ser modificados por el usuario propietario, mientras que el resto de usuarios/grupos no tendrán ningún permiso. Muestra la configuración realizada.
8. Desde un cliente Windows (como usuario propietario) intenta acceder a la carpeta ejercicio2 y crear un documento llamado “miTesoro.txt”. Acto seguido, vuelve al servidor e intenta modificar el fichero (**SIN SER SUDO**) ¿Existe algún problema? ¿Por qué?
9. Ahora desde el cliente (Windows) crea un directorio llamado “Mairon”. Vuelve al servidor e intenta crear un fichero “Sauron.txt” dentro de Mairon (**SIN SER SUDO**). ¿Qué ocurre? ¿Qué permisos tiene la carpeta “Mairon”?.
<!-- 10. Busca en Internet que significa la etiqueta [global] al comienzo del fichero de configuración de samba `/etc/samba/smb.conf` -->