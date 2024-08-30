# SAMBA DOMAIN CONTROLLER

<figure>
  <img src="./imagenes/067/001P.png" width="850"/>
</figure>

## Actividades tipo Proyecto (AEA)

### PR61. Introducción y configuración de Samba DC

Tal y como se plantea en la situación de aprendizaje trabajas para el departamento TIC de **ASOps**, y acaban de aparecer los siguientes proyectos para la integración de Samba DC.

Instalación y configuración de Samba DC en los siguientes servidores Linux:

- Red Hat.
- Centos.
- Debian.
- Fedora.
- OpenSuse

Debes de elegir la distribución a implementar **Samba DC** y desarrollar con tu grupo para poder loguear clientes Windows y compartir recursos entre ellos. Tareas a realizar:

- Desarrollad un informe con las capturas explicadas de los principales pasos de esta guía y sus comprobaciones.
- Añadid un usuario mediante Webmin. Reinicia W10 y logueate con este nuevo usuario.
- Eligid de entre estas dos distribuciones de Linux Red Hat, Manjaro o Fedora para añadir el cliente al AD y loguearte con alguno de los usuarios configurados.
- Genera una estructura de carpetas de empresa (a desarrollar) de los diferentes departamentos y aplica niveles de seguridad de acceso a los mismos.

!!! note "Nota"
    Añadid capturas con sus correspondientes aclaraciones en cada aparatado.

## Guía Samba DC Ubuntu Server

Los objetivos de esta guía son:

- Preparación del servidor.
- Instalar el Controlador de Dominio en Samba.
- Acceder y Crear clientes de windows.
- Activar la aplicación de gestión RSAT.
<!-- - Configurar los recursos compartidos. -->

[Webgrafía](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller#Provisioning_Samba_AD_in_Interactive_Mode)

## Recordatorio Controlador de Dominio (DC).

- Es un **servidor** que se ocupa de permitir el acceso de determinados usuarios a recursos compartidos del dominio.
- Un **dominio** es un grupo de recursos (equipos, ficheros, usuarios, impresoras, etc.) dentro de una red. El acceso a dicho dominio o las políticas de seguridad de todos esos recursos es administrada por el controlador de dominio.
- Habitualmente se implementan en Windows Server, aunque también se pueden implementar en Linux bajo samba.
- En una implementación adecuada, se despliega más de un controlador de dominio a la vez que actúan como uno (cluster) de esta forma se dota al **servicio de redundancia y alta disponibilidad**, es decir, que si se produce una avería en un control de dominio el servicio sigue activo pues hay otro/s controladores activos.
- Los dominios usan rutas de nombres similares a los DNS, tienen una ruta absoluta **FQDN (Full
Qualified Domain Name)** y una ruta relativa **PQDN (Partially Qualified Domain Name)**.

!!! note "**NOTA:**"
    Independientemente del sistema operativo usado necesitaremos de otros servicios para complementar, a continuación se enumeran:

- **LDAP**: Será la base de datos que alberga la información de los usuarios.
- **Kerberos**: Se encarga de la seguridad a nivel de autentificación segura de los usuarios.
- **DNS**: Habitualmente el acceso al dominio se hace a través de un nombre de dominio. El
servicio DNS permite la resolución de ese nombre de dominio al direccionamiento IP.
- **NTP**: Es el servicio que se encarga de la sincronización del reloj de cada máquina.

## Preparación

Antes de la instalación del **Controlador de Dominio o Domain Controler DC** mediante `Samba`, es necesaria, la preparación del servidor a nivel de red y dominio. Para ello se detallan los siguientes pasos:

1. Restaura un **snapshot** "limpio" de tu máquina virtual de `Ubuntu 22.04` y se realiza un update.

<figure>
  <img src="./imagenes/067/000G.gif"/>
  <figcaption>Comprobación versión Ubuntu.</figcaption>
</figure>

2. Configura la máquina virtual en formato “**Nat Network**”.


<!-- 3. Establecemos una configuración de red para tu servidor.
    1. Se establece una IP estática en el servidor del rango configurado en la red NAT, en este caso se realiza editando el fichero `nano /etc/netplan/00-installer-config.yaml` de la siguiente forma:

``` bash
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.2.2/24
      nameservers:
        search: [aso.com]
        addresses: [8.8.8.8, 8.8.4.4]
      routes:
        - to: default
          via: 192.168.2.1
  version: 2
```

<figure>
  <img src="./imagenes/067/002P.png"/>
  <figcaption>Configuración de IP estática en Red NAT.</figcaption>
</figure>

!!! tip "A tener en cuenta"
    - En la configuración del archivo de red se crea un parámetro denominado `search`, el cual se configura para realizar un **dominio de búsqueda** (**Search Domain**), el cual nos va a permitir que si el servidor recibe una búsqueda de petición de un recurso, por ejemplo “maquina03”, automáticamente el servidor va intentar buscar por el nombre de dominio completo: “maquina01.aso.com”. Es decir, intenta resolver una ruta relativa de dominio (`PQDN`) a una ruta absoluta de dominio (`FQDN`).
    -Recordatorio: se debe de aplicar los cambios con **netplan apply**. -->


<!-- <figure>
  <img src="./imagenes/07/072/007.png" width="550"/>
</figure> -->

<!-- 3. Instala los paquetes “`dnsutils`”.
    1. El paquete `dnsutils` simplemente es una serie de herramientas que ayudan a comprobar la resolución del nombre de nuestro dominio es correcta.

``` bash
sudo apt-get update -y
sudo apt-get dnsutils
```

<figure>
  <img src="./imagenes/067/001G.gif"/>
  <figcaption>Instalación dnsutils.</figcaption>
</figure> -->
    
<!-- 5. Es conveniente saber cuál es el nombre de nuestra máquina:

<figure>
  <img src="./imagenes/07/072/008.png" width="550"/>
</figure> -->

3. A continuación se establece el nombre de host del servidor este caso `profesor.aso.local` y se añade el servidor al archivo de hosts.

!!! warning "**CUIDADO**"
    **Se debe cambiar profesor por el nombre de la alumna o alumno.**

3.1. Cambio de nombre de hosts a profesor.

``` bash
hostnamectl set-hostname profesor
```

3.2. El fichero `/etc/hosts` es como un servicio DNS local, es decir, tiene una serie de pares `nombres/IP` que resuelve localmente.
  1. Realiza una copia de seguridad del fichero `/etc/hosts`.
  2. Modifica el fichero original (`/etc/hosts`) para que resuelva el nombre de tu máquina y tu FQDN en este caso `profesor.aso.local`.
  3. Comprueba con un ping a los nombres que funciona y te contesta con la IP.

!!! example "Por ejemplo"
    Inicialmente En este caso el servidor se llama aso, por lo tanto si se realiza un ping a `aso` antes de modificar el /etc/hosts la máquina debe saber resolverlo a la IP `127.0.1.1` que por defecto en ubuntu se utiliza para determinar la dirección IP que corresponde al nombre de host.

- En este caso el fichero modificado quedaría:

``` bash
127.0.0.1 localhost
192.168.2.4 profesor.aso.local profesor

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

!!! warning "Cuidado"
    La nueva IP es la recibida en el servidor del `DHCP` de la RedNAT configurada.

3.3. Ejecución y comprobación.

``` bash
# verifica FQDN
hostname -f

# verifica FQDN si resuelve a la IP del servidor
ping -c3 profesor.aso.local
```

<figure>
  <img src="./imagenes/067/002G.gif"/>
  <figcaption>Modificación del <b>/etc/hosts</b> y comprobaciones de ping.</figcaption>
</figure>

4. Se configura la resolución de nombres en el archivo `resolv.conf` encargado de redireccionar las peticiones de `DNS`.

!!! tip "A tener en cuenta"
    - Para realizar un **dominio de búsqueda** (**Search Domain**), el cual nos va a permitir que si el servidor recibe una búsqueda de petición de un recurso, por ejemplo “maquina01”, automáticamente el servidor va intentar buscar por el nombre de dominio completo: “maquina01.aso.com”. Es decir, intenta resolver una ruta relativa de dominio (`PQDN`) a una ruta absoluta de dominio (`FQDN`).
    - **Recordatorio**: se debe de aplicar los cambios con **netplan apply**.

a. Se desactiva el servicio de resolución de DNS.

``` bash
# Para y desactiva el servicio de resolución de DNS
sudo systemctl disable --now systemd-resolved

# Elimina el link simbólico de /etc/resolv.conf
sudo unlink /etc/resolv.conf
```

b. Se crea un nuevo `resolv`

``` bash
# Crea un nuevo archivo /etc/resolv.conf
nano /etc/resolv.conf
```

c. Se introduce las siguientes líneas en el archivo nuevo.

``` bash
# Samba server IP address
nameserver 192.168.2.4

# fallback resolver Quad9
nameserver 9.9.9.9

# Dominio principal de Samba
search aso.local
```

!!! warning "Cuidado"
    La IP es la recibida en el servidor del `DHCP` de la RedNAT configurada

d. Se hace inmutable el archivo para evitar que cualquier instalación de otros servicios lo modifique automáticamente.

``` bash
# add attribute immutable to the file /etc/resolv.conf
sudo chattr +i /etc/resolv.conf
```

<figure>
  <img src="./imagenes/067/008G.gif"/>
  <figcaption>Configuración de resolución de nombres.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/07/072/009.png" width="850"/>
  <figcaption>Ping a profesor.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/010.png" width="850"/>
  <figcaption>Comprobación `/etc/hosts`.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/011.png" width="850"/>
  <figcaption>Comprobación de ping al localhost debería contestar 127.0.0.1.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/012.png" width="850"/>
  <figcaption>Comprobaciónes de ping.</figcaption>
</figure> -->

<!-- 7. A continuación instalamos los paquetes “**attr**” y “**acl**” que nos darán más opciones de seguridad y mayor compatibilidad al interconectar con equipos clientes Windows a nuestro dominio.

``` bash
sudo apt-get install attr acl
```

<figure>
  <img src="./imagenes/067/003G.gif"/>
  <figcaption>Instalación de los paquetes <b>attr</b> y <b>acl</b>.</figcaption>
</figure>

8. En el siguiente paso se aplican estos paquetes al **sistema cuando se arranque**. Para ello, Se realiza una copia de seguridad del fichero `/etc/fstab` y posteriormente lo editamos. **Importante** que el separador en este fichero es el tabulador.
    1. Simplemente hay que añadir en el raíz (“/”) las opciones **user_xattr,acl,barrier=1,defaults** donde antes sólo ponía `defaults 0 1`.

``` bash
user_xattr,acl,barrier=1,defaults
```

!!! warning advertencia
    - Importante no equivocarse y hacerlo en el “/”, no en el `/boot` ni en la memoria **swap**.
    - Reiniciar el server (reboot).

<figure>
  <img src="./imagenes/067/004G.gif"/>
  <figcaption>Aplicar <b>attr</b> y <b>acl</b> al sector de arranque.</figcaption>
</figure>

<!-- - **Ejemplos:** -->

<!-- <figure>
  <img src="./imagenes/07/072/013.png" width="750"/>
  <figcaption>attr y acl acl al sector de arranque en /etc/fstab.</figcaption>
</figure> -->

<!-- 9. Se instala el paquete **NTP**, ya que es necesario tener sincronizada la hora para servicios del estilo **firewall**, **LDAP**, para los registros de los logs, para el servicio activo de Windows, etc.
    1. Se puede comprobar que el servicio está activo con `“systemctl status ntp”`

``` bash
sudo apt-get install ntp
```

<figure>
  <img src="./imagenes/067/005G.gif"/>
  <figcaption>Instalación y comprobación <b>NTP</b>.</figcaption>
</figure> -->

## Instalación Samba-DC

<!-- 10. **Detenemos y deshabilitamos** cualquier servicio que tuviéramos de samba (que no fuera de
    controlador de dominio) previa a la instalación.

<figure>
  <img src="./imagenes/07/072/014.png" width="750"/>
</figure>

11. Eliminamos cualquier configuración previa de samba o de kerberos.

apt-get install -y acl attr samba samba-dsdb-modules samba-vfs-modules smbclient winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user dnsutils chrony net-tools -->

1. Se instalan los siguientes paquetes:

``` bash
sudo apt-get update -y
sudo apt-get install -y acl attr samba samba-dsdb-modules samba-vfs-modules smbclient winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user chrony net-tools
```

!!! abstract "Donde se destacan los siguientes:"
    1. **Samba** &#8594 En el paquete ya viene integrado un módulo de `LDAP` para almacenar los usuarios.
    2. **krb5-config** &#8594 Es la configuración de `Kerberos` que usaremos en samba. Habrá que instalarlo más adelante.
    3. **winbind** &#8594 Paquete necesario para que un cliente de `Windows` se pueda autentificar en nuestro sistema DC en Linux.
    4. **smbclient** &#8594 Se instala el cliente samba, simplemente para tener ciertos comandos de consulta, realmente no es necesario para montar el servidor.

!!! tip "Durante la instalación del **Kerberos** nos pedirá 3 cosas principalmente:"
    1. **El reino (realm)**: se indicará el mismo que el dominio configurado en el archivo de configuración de red.
    2. **La IP del servidor Kerberos**: FQDN.
    3. **La IP para la administración del Kerberos**: FQDN.

!!! note "**Nota**" 
    En este caso el servidor de kerberos va a estar en el propio controlador de dominio, pero en otra situación se podría tratar de una máquina independiente.

<figure>
  <img src="./imagenes/067/006G.gif"/>
  <figcaption>Instalación samba y paquetes necesarios.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/07/072/015.png" width="750"/>
</figure> -->

2. A continuación se necesita detener varios servicios instalados con anterioridad y que ahora serán
gestionados a través de Samba. Se paran y se deshabilitan.

``` bash
# stop and disable samba services - smbd, nmbd, and winbind
sudo systemctl disable --now smbd nmbd winbind
```

3. Se activa el servicio **samba-ad-dc** mediante los siguientes comandos:

``` bash
systemctl unmask samba-ad-dc
systemctl enable samba-ad-dc
```

<figure>
  <img src="./imagenes/067/007G.gif"/>
  <figcaption>Instalación samba y paquetes necesarios.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/07/072/016.png" width="750"/>
</figure> -->

## Configuración Samba-DC

1. Antes de pasar a configurar Samba, es conveniente guardar el fichero de configuración original, por si hubiera que retomar la instalación desde este punto:

``` bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.copia
```

2. El siguiente paso es configurar el controlador de dominio de `Samba` mediante el uso de samba-tool.

``` bash
samba-tool domain provision --use-rfc2307 --interactive
```

!!! note "Nota"
    - Dentro de la configuración cogerá el **reino/dominio** del servidor, por lo tanto no se tiene que rellenar nada, **excepto la parte de DNS forwarder que se usará Quad9, el cual es DNS recursivo público global que tiene como objetivo proteger a los usuarios del malware y el phishing. (9.9.9.9)**.
    - La razón de esta configuración es porque el servidor actuará como DNS, pero si no se puede resolver una página pasaremos la solicitud de resolución al servidor DNS forwarder (9.9.9.9).

!!! warning "Advertencia"
    Como **contraseña** de administrador necesita tener una **mayúscula/minúscula/número/longitud de 8**. El usuario por defecto es “administrator”, en este caso se usará **AsoAula5**

- Si todo ha salido bien, se creará el fichero `smb.conf` acorde con la configuración:

<figure>
  <img src="./imagenes/067/009G.gif"/>
  <figcaption>Configuración Samba y comprobación.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/07/072/017.png" width="750"/>
</figure> -->

- **[global]**: Donde se puede observar todos los datos de configuración del dominio, además de los
roles para los que se ha creado (DC-AD).
- **[netlogon]**: Ya ha creado de forma automática la carpeta para los scripts de inicio de sesión de
los clientes.
- **[sysvol]**: El sysvol es reponsable de replicar las GPOs (Políticas de Grupos) aplicadas en el editor
de directivas de grupo, todos los cambios y GPOs nuevos son replicados por allí.

3. Ahora se instala Kerberos, antes solo se había asociado la configuración a `Samba` (reino, dns, password, etc). Para ello se realiza una copia de seguridad de la configuración actual y se sobrescribe la realizada por Samba.

``` bash
# Renombra la configuración por defecto de Kerberos a krb5.conf.original
sudo mv /etc/krb5.conf /etc/krb5.conf.original

# Copia la configuración generada samba-tool
sudo cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```

<figure>
  <img src="./imagenes/067/010G.gif"/>
  <figcaption>Configuración Kerberos.</figcaption>
</figure>

4. Se incializa el servicio de Samba

``` bash
# start samba-ad-dc service
sudo systemctl start samba-ad-dc

# verify samba-ad-dc service
sudo systemctl status samba-ad-dc
```

<figure>
  <img src="./imagenes/067/011G.gif"/>
  <figcaption>Inicia Samba y comprueba.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/07/072/018.png" width="750"/>
</figure> -->

5. Se configura la sincronización del tiempo para que el servidor este en hora, con el objetivo de realizar de forma adecuada tareas de resolución de incidencias.

5.1. Se establecen los permisos para NTP (Network Time Protocol). En la instalación de Samba se utilizó el paquete `chrony`, el cual es una implementación del Network Time Protocol. Es una alternativa a ntpd, que es una implementación de referencia de NTP. A continuación se muestran los comando para dar los permisos adecuados a `chrony`.

``` bash
# Permite al grupo _chrony leer en el directorio ntp_signd
sudo chown root:_chrony /var/lib/samba/ntp_signd/

# Cambia los permisos en el directorio ntp_signd.
sudo chmod 750 /var/lib/samba/ntp_signd/
```

5.2 Configuración de `Chrony`, dentro del archivo de configuración `/etc/chrony/chrony.conf` se añaden las siguientes líneas:

``` bash
# bind el servicio de chrony a la dirección IP del Samba AD.
bindcmdaddress 192.168.2.4

# Permite a los clientes de la red conectar al Chrony NTP server en este caso:
allow 192.168.2.0/24

# Especifica el directorio del ntpsigndsocket para el Samba AD.
ntpsigndsocket /var/lib/samba/ntp_signd
```

5.3 Se reinicia el servicio y se comprueba su estado.

``` bash
# Reinicia el servicio chronyd.
sudo systemctl restart chronyd

# Verifica el estado de chronyd
sudo systemctl status chronyd
```

<figure>
  <img src="./imagenes/067/012G.gif"/>
  <figcaption>Instalación y configuración de <b>chronyd.</b></figcaption>
</figure>

## Comprobación del Samba Active Directory

1. Verificaciones del dominio.

``` bash
# Verifica el Dominio
host -t A aso.local

# Verifica el FQDN
host -t A profesor.aso.local
```

2. Comprobación de `Kerberos` y `ldap`.

``` bash
# Comprobación del registro SRV para _kerberos. 
host -t SRV _kerberos._udp.aso.local

# Comprobación del registro SRV para _ldap
host -t SRV _ldap._tcp.aso.local
```

!!! note "NOTA"
    El registro `SRV` es un registro de recursos del sistema de nombres de dominio (`DNS`). Se usa para identificar equipos que hospedan servicios.

3. Comprobar el `login` con el usuario `administrator` con el comando `kinit`.

``` bash
# Autenticate a Kerberos con el usuario administrator.
kinit administrator@ASO.LOCAL

# Verifica la lista de accesos.
klist
```

!!! warning "Cuidado"
    A tener en cuenta las mayúsculas del dominio en el comando `kinit`.

<figure>
  <img src="./imagenes/067/013G.gif"/>
  <figcaption>Comprobaciones</figcaption>
</figure>

## Creación y acceso de usuarios

Existen diferentes formas de crear usuarios, se destacan dos:

1. Mediante comandos de `Samba`.
2. A través de la herramienta de gestión `RSAT`.

A continuación se describen ambos métodos:

### Crear usuario con samba-tool

- En este caso se utiliza los comandos de samba-tool para crear el usuario:

``` bash
# Crea un nuevo usuario en Samba.
sudo samba-tool user create javi

# Comprueba los usuarios de Samba.
sudo samba-tool user list
```

!!! note "Nota"
    Como se ha explicado en secciones anteriores los usuarios creado en `Samba` deben estar creados también en el sistema.

## Acceso al Samba-DC desde un Windows Client

### Configuración de DNS y comprobación

- Se accede al cliente, en este caso **Windows 10** y se configuran los `DNS` del `Samba-DC`.

<figure>
  <img src="./imagenes/067/014G.gif"/>
  <figcaption>Configuración DNS de Samba-DC en WinClient.</figcaption>
</figure>

- Por otro lado se pueden comprobar los DNS mediante comandos de PowerShell.

``` pwsh
# En Powershell
Get-DnsClientServerAddress

# ping al AD domain profesor.aso.local
ping profesor.aso.local

# ping al AD domain aso.local
ping aso.local
```

<figure>
  <img src="./imagenes/067/015G.gif"/>
  <figcaption>Comprobación DNS con PowerShell.</figcaption>
</figure>

### Añadir cliente al Active Directory con PowerShell

- El siguiente paso es añadir el Cliente al Controlador de Dominio, y en este caso lo vamos a hacer por PowerShell con el siguiente comando:

``` pwsh
# Añade Windows 10 al Active Directory usando POWERSHELL
Add-Computer -DomainName "aso.local" -Restart
```
<figure>
  <img src="./imagenes/067/016G.gif"/>
  <figcaption>Añadir cliente al dominio mediante PowerShell.</figcaption>
</figure>

!!! note "Nota"
    Al reiniciar debe aparecer el login para el Dominio.

- Comprobación de usuarios en el DC desde el Windows Client.

<figure>
  <img src="./imagenes/067/017G.gif"/>
  <figcaption>Comprobación usuarios desde W10.</figcaption>
</figure>

## Descargar y Activar la Aplicación RSAT

**RSAT** (Remote Server Administration Tools) es una aplicación cuyo objetivo es administrar el servidor
pensada para instalarse en clientes que replica las herramientas disponibles en el controlador del dominio.

La aplicación se puede descargar del siguiente enlace [RSAT](https://www.microsoft.com/es-ES/download/details.aspx?id=45520)

- Se descarga e instala:

<figure>
  <img src="./imagenes/067/018G.gif"/>
  <figcaption>Descarga e instalación de RSAT en W10.</figcaption>
</figure>

!!! note "Nota"
    - Se debe reiniciar para que aparezca en herramientas administrativas, las aplicaciones del AD.

- Otra opción de instalación de diferentes paquetes.

<figure>
  <img src="./imagenes/067/019G.gif"/>
  <figcaption>Herramientas del AD.</figcaption>
</figure>

<figure>
  <img src="./imagenes/067/020G.gif"/>
  <figcaption>Herramientas del AD II.</figcaption>
</figure>

## Acceso Samba-DC desde un fedora

!!! warning "Ubuntu Server"
    En el Ubuntu Server es necesario añadir repositorios.

1. **En Ubuntu Server**, es necesario añadir los repositorios del "Universe".

``` bash
sudo add-apt-repository universe
sudo add-apt-repository multiverse
sudo apt update
```

!!! warning "Cliente"
    En el cliente.

2. Se Establece nombre de host.

``` bash
sudo hostnamectl set-hostname fedora.aso.local
```

3. En Fedora, se edita la configuración Resolved.

``` bash
nano /etc/systemd/resolved.conf

# Añade las siguientes líneas
[Resolve]
DNS=192.168.2.4 9.9.9.9 8.8.8.8

# Guarda y reinicia el servicio.
sudo systemctl restart systemd-resolved
```

!!! warning "Ubuntu Server"
    De nuevo en el Ubuntu Server es necesario añadir unos paquetes.

4. Se Instalan los paquetes necesarios (**solo Ubuntu**)

``` bash
sudo apt -y install realmd libnss-sss libpam-sss sssd sssd-tools adcli samba-common-bin oddjob oddjob-mkhomedir packagekit
```

!!! warning "Cliente"
    Ya en el cliente.

5. (Opcional) Descubrir dominio.

``` bash
sudo realm discover aso.local
```

6. Se añade al dominio.

``` bash
sudo realm join -U Administrator aso.local
```

!!! note "Nota"
    Al reiniciar el cliente se podrá iniciar sesión como usuario del dominio.



<!-- -------------------------------------------------------------------------------------------------------- -->
<!-- 17. A continuación copiamos la configuración de kerberos para samba por la configuración por defecto de kerberos:

``` yaml
cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
```

18. Detemos system-resolved para que no nos manipule automáticamente el fichero `/etc/resolv.conf`

<figure>
  <img src="./imagenes/07/072/019.png" width="750"/>
</figure>

19. Finalmente comprobamos que la configuración de Kerberos está bien:

<figure>
  <img src="./imagenes/07/072/020.png" width="750"/>
</figure>

20. Como ya tenemos configurado el DNS forwarder, volvemos a nuestra configuración de red y cambiamos el servidor DNS como a nosotros mismos, para resolver las peticiones de nuestro dominio. Acordaros del netplan apply.

<figure>
  <img src="./imagenes/07/072/021.png" width="750"/>
</figure>

Finalmente podemos ver el resumen de nuestro dominio:

<figure>
  <img src="./imagenes/07/072/022.png" width="750"/>
</figure>

Comprobamos que funciona la resolución al dominio:

<figure>
  <img src="./imagenes/07/072/023.png" width="750"/>
</figure>


## Agregar un cliente Windows al dominio de Samba.

### Crear Usuario en el Controlador de Dominio

Lo primero será tener un usuario válido para acceder al dominio, para ello vamos a nuestro servidor que hace de controlador de dominio y creamos dicho usuario:

!!! tip "**Redorcatorio**"
    Recuerda a la hora de insertar la contraseña que debe cumplir con un mínimo de 8 caracteres, una mayúscula, una minúscula y un número.

<figure>
  <img src="./imagenes/07/072/024.png" width="850"/>
</figure>

- Para el usuario “niko” se ha creado la contraseña “Niko1234#”

<figure>
  <img src="./imagenes/07/072/025.png" width="750"/>
</figure>

!!! note "**NOTA:**"
    A diferencia de lo que pasaba en samba, cuando NO era un controlador de dominio, el usuario no es necesario que exista en el sistema y además los UID no son iguales a los del sistema operativo.

<figure>
  <img src="./imagenes/07/072/026.png" width="550"/>
</figure>

### Añadir el Cliente Windows al Dominio Samba.

1. Añade al cliente en la misma red interna que el controlador de dominio de SAMBA, en nuestro caso una `“NAT Network”`. Ésta red se le asignará una IP automática y una puerta de enlace, pero debemos forzarle que el servidor DNS sea nuestro controlador de dominio.

!!! note "**NOTA:**"
    En una situación real, el propio controlador de dominio actuaría de servidor DHCP, o en el caso de tener un servicio DHCP ya asignaría el DNS automáticamente del controlador de dominio, por lo que no habría que hacer este paso.

<figure>
  <img src="./imagenes/07/072/027.png" width="550"/>
  <figcaption>Configuración de Propiedades de Red en Windows.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/028.png" width="550"/>
  <figcaption>Comprobación conexión con Server.</figcaption>
</figure>

2. Vamos a introducir el cliente dentro de nuestro dominio:
  
  - Botón derecho – Propiedades sobre el icono de `“Equipo”`.
  - Pinchar en `“Cambiar Configuración”`.
  - Posteriormente le damos a `“cambiar..”` y seleccionamos nuestro dominio.

<figure>
  <img src="./imagenes/07/072/029.png" width="850"/>
  <figcaption>Propiedades Equipo.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/030.png" width="850"/>
  <figcaption>Cambio para sellecionar dominio.</figcaption>
</figure>

!!! note "**NOTA:**"
  Es muy importante que accedamos con un usuario con permisos de administrador en el servidor linux, si os acordáis Kerberos es el servicio que se encarga de la seguridad de nuestro controlador de dominio, y por defecto éste crea el usuario `“administrator”`.

<figure>
  <img src="./imagenes/07/072/031.png" width="550"/>
  <figcaption>Cambio de usuario a Administrador.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/032.png" width="850"/>
  <figcaption>Confirmación cambio Dominio.</figcaption>
</figure>

- Finalmente reiniciamos el equipo cliente e intentamos acceder con un usuario del controlador de dominio, como por ejemplo “niko”, fijaros que es un usuario que no existe en el Windows a nivel local, existe en el servidor, por lo que “niko” se podría registrar en cualquier equipo asociado al dominio.

<figure>
  <img src="./imagenes/07/072/033.png" width="550"/>
  <figcaption>Comprobación de Logueo.</figcaption>
</figure>

## Compartir recursos en un dominio entre Windows y Servidor Ubuntu.

A la hora de compartir recursos es prácticamente igual a cuando se realizaba en Samba sin directorio activo.

1. Crear la carpeta a compartir y darle permisos requeridos.
2. Acceder al fichero de configuración de samba.
3. Generar un recurso a compartir.

<figure>
  <img src="./imagenes/07/072/034.png" width="350"/>
  <figcaption>Ejemplo recurso compartido.</figcaption>
</figure>

4. Reiniciar el servicio.
  1. Ahora el servicio de **samba** esta configurado como directorio activo podemos hacer:

``` yaml
smbcontrol all reload-config
```

- Para que cualquier usuario del dominio pueda administrar un recurso podemos agregarle al recurso el grupo `“Domain Users”`, se puede comprobar que existe con el siguiente comando dentro de la base de datos de Samba.

<figure>
  <img src="./imagenes/07/072/035.png" width="750"/>
</figure>

### winbind

El problema que tendremos, la primera vez, es que ese grupo no tiene un **GID (identificador de grupo)** asociado del sistema operativo Ubuntu Server. Para ello vamos a usar `“winbind”` para que pueda traducir a partir de un grupo de samba a un grupo del sistema y hacer una equivalencia.

- Se instalan los paquetes:

``` yaml
apt-get install libnss-winbind
apt-get install libpam-winbind
```

- Se modifica el fichero `“/etc/nsswitch.conf”` que revisa donde tiene que buscar el registro de `usuarios/grupos` del sistema. “Files” será su primera opción, que será `/etc/passwd` y `/etc/groups`, si no los encuentra ahí pasa a usar el servicio winbind para hacer la equivalencia con samba AD.

<figure>
  <img src="./imagenes/07/072/036.png" width="750"/>
</figure>

- Finalmente se puede comprobar que funciona y que cuando le preguntamos al sistema por el grupo del controlador de dominio `“Domain Users”` el sistema nos indica que es capaz de relacionarlo con un identificador de grupo, en este caso el `100`.

<figure>
  <img src="./imagenes/07/072/037.png" width="750"/>
</figure>

- En el ejemplo se ve que el usuario del dominio niko pertenece al grupo 100 que es el de domain users.

<figure>
  <img src="./imagenes/07/072/038.png" width="750"/>
</figure>

- Finalmente se añaden todas las carpetas a compartir y sus rutas padres al grupo del dominio:

``` yaml
chgrp -R "Domain Users" /recursosSOR
chmod 2750recursosSOR
```

!!! note "**NOTA:**"
    Si se había iniciado sesión con tu usuario de Windows 10 antes de cambiar la configuración de `/etc/nsswitch`, cierra sesión y vuelve a acceder.

- En el `cliente Windows 10` que ya está unido al dominio deberemos:

1. Comprobar que navega por Internet.
2. Acceder a una `carpeta/directorio` y buscar el nombre de tu equipo que está compartiendo el recurso, en este caso es el propio servidor.

<figure>
  <img src="./imagenes/07/072/039.png" width="850"/>
</figure>

!!! note "**NOTA:**"
    No debería pedir credenciales, porque ya habíamos iniciado sesión como “niko” en este ordenador que pertenece al dominio y a Domain Users.

- Además, desde las propiedades de seguridad del cliente Windows podemos cambiar las **políticas de acceso de esa carpeta compartida** (siempre que tengamos permisos en el servidor para ello).

<figure>
  <img src="./imagenes/07/072/040.png" width="850"/>
</figure>

## Activar RSAT

Se debe aplicar a `Aplicaciones y Características` – `Características Opcionales` y se agrega una nueva característica:

<figure>
  <img src="./imagenes/07/072/041.png" width="850"/>
  <figcaption>Características Opcionales.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/042.png" width="550"/>
  <figcaption>Agregar una Característica.</figcaption>
</figure>

- Para agregar filtramos por **RSAT** y activamos:

<figure>
  <img src="./imagenes/07/072/043.png" width="850"/>
  <figcaption>Agregar RSAT.</figcaption>
</figure>

## Perfiles Móviles.

Un perfil móvil, es aquel donde un usuario se registre en la máquina que se registre, si ésta pertenece al dominio, tendrá sus ficheros personales con los que trabaja día a día.

### Configuración de Políticas de Cliente

Como administradores, si queremos implementar perfiles móviles, tenemos dos opciones.

1. Indicar que el perfil de cada usuario debe apuntar al servidor, en nuestro caso el **samba ubuntu server**.
2. Crear una política de grupo para que cada usuario que acceda a dicha máquina directamente se le genere el perfil adecuado.

!!! note "**NOTA:**"
    Obviamente, la solución b) es mucho más escalable y por tanto más adecuada.

#### OPCIÓN 1

1. Entra al cliente Windows como Administrador (**Administrator**).
2. Por curiosidad, ahora puedes ir a `“Usuarios y Equipos de Active Directory”` y ver/gestionar los grupos del directorio activo desde el propio Windows.
3. En las propiedades del usuario y luego en su perfil:

<figure>
  <img src="./imagenes/07/072/044.png" width="850"/>
  <figcaption>Usuarios y Equipos.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/045.png" width="550"/>
  <figcaption>Propiedades niko.</figcaption>
</figure>

#### OPCIÓN 2

1. Entra al cliente Windows como Administrador (**Administrator**).
2. Accede a la administración de directivas de grupo (GPO).
3. Crea una **GPO** asociado al dominio.

<figure>
  <img src="./imagenes/07/072/046.png" width="850"/>
  <figcaption>Creamos GPO.</figcaption>
</figure>

4. Editamos la nueva política creada.

<figure>
  <img src="./imagenes/07/072/047.png" width="550"/>
  <figcaption>Editar nueva política.</figcaption>
</figure>

5. Ahora Navegamos por `Configuración del Equipo` &#8594 `Directivas` &#8594` Plantillas Administrativas` &#8594 `Sistema` &#8594 `Perfiles de Usuario.`

- Dentro de Perfiles de Usuario seleccionamos la plantilla sobre perfiles móviles para todos los usuarios conectados al equipo.

<figure>
  <img src="./imagenes/07/072/048.png" width="850"/>
  <figcaption>Establecer Perfil móvil.</figcaption>
</figure>

<figure>
  <img src="./imagenes/07/072/049.png" width="850"/>
  <figcaption>Ruta para Perfil móvil.</figcaption>
</figure>

- Una vez modificado la política, ésta se actualiza en el servidor (Ubuntu Server) n la ruta `Sysvol`. Es decir si vamos al fichero de configuración de Samba, vemos que hay un recurso compartido llamado `sysvol`.

<figure>
  <img src="./imagenes/07/072/050.png" width="450"/>
  <figcaption>Ruta sysvol.</figcaption>
</figure>

- Si investigamos dentro de esa carpeta vemos que hay una sección de Políticas que justo se ha creado al mismo momento que hemos aplicado las políticas:

<figure>
  <img src="./imagenes/07/072/051.png" width="950"/>
  <figcaption>Confirmación Ruta.</figcaption>
</figure>

### Confirmación de Recursos

- Se debe crear la carpeta donde se almacenarán los recursos de los usuarios y Configurar acceso a cualquier usuario perteneciente al grupo de administradores del dominio:

<figure>
  <img src="./imagenes/07/072/052.png" width="850"/>
</figure>

- Posteriormente lo agregamos como un recurso a compartir:

<figure>
  <img src="./imagenes/07/072/053.png" width="350"/>
</figure>

- Reiniciamos el servicio:

``` yaml
smbcontrol all reload-config
```

- Desde Windows (que es más visible) gestionamos los permisos de los perfiles de los usuarios, para ello accedemos a `\\profesor` y a las propiedades de “profiles”:

Si queremos agregar algún grupo/usuario que no esté, como podría ser Domain Users:

<figure>
  <img src="./imagenes/07/072/054.png" width="850"/>
</figure>

- Una vez tengamos agregados todos los grupos que queremos , vamos a filtrar e indicar los permisos de cada uno.

<figure>
  <img src="./imagenes/07/072/055.png" width="550"/>
</figure>

- Opciones de Compartición:

<figure>
  <img src="./imagenes/07/072/056.png" width="850"/>
</figure>

- Opciones de Seguridad:

<figure>
  <img src="./imagenes/07/072/057.png" width="850"/>
</figure>

<figure>
  <img src="./imagenes/07/072/058.png" width="850"/>
</figure>

### Acceso desde el usuario

Ahora cada vez que nos registremos en cualquier ordenador del dominio, aunque sea la primera vez, ya tendremos configurada una carpeta compartida en nuestro perfil para nosotros solos (y los administradores).

<figure>
  <img src="./imagenes/07/072/059.png" width="850"/>
</figure>

### Redirección

Entre sistemas Linux sí se podía direccionar directamente el `/home` de un usuario a una carpeta del servidor `[homes]`. En el caso de Windows debemos realizar una redirección.

1. Accedemos como “**administrator**”.
2. Accede a la administración de directivas de grupo (**GPO**).
3. Crea una **GPO** asociado al dominio.

<figure>
  <img src="./imagenes/07/072/060.png" width="850"/>
</figure>

4. Editamos la nueva política creada.

<figure>
  <img src="./imagenes/07/072/061.png" width="550"/>
</figure>

5. Accedemos a las propiedades de la carpeta que queremos redireccionar:

<figure>
  <img src="./imagenes/07/072/062.png" width="850"/>
</figure>

<figure>
  <img src="./imagenes/07/072/063.png" width="850"/>
</figure>

6. Cerramos sesión y Registramos con otro usuario no administrador.

Una vez registrados creamos unos ficheros en el escritorio o carpetas, como si estuvieramos trabajando y cerramos la sesión.

Al cerrar la sesión se migran los ficheros al servidor y si nos conectáramos a cualquier otra máquina tendríamos nuestro Escritorio y ficheros con nosotros.

<figure>
  <img src="./imagenes/07/072/064.png" width="850"/>
</figure>

!!! tip "**A tener en cuenta**"
    Obviamente en la empresa, en caso de tener un AD con Samba como DC, no hará falta configurar cada cliente Windows (crear las políticas, etc), si no que usaremos una imagen, creada por ejemplo con clonezilla, y distribuiremos esa imagen con las políticas ya configuradas a todas las máquinas. -->

