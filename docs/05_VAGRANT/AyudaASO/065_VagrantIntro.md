# Despliegue de Sistemas Heterogéneos con Vagrant

## Justificación

Actualmente el auge del desarrollo de aplicaciones en sistemas virtualizados requiere del despliegue, conectividad y flexibilidad de sistemas heterogéneos en escenarios de desarrollo de trabajo y producción.

Una de las herramientas más utilizadas por los SRE (Site Reliability Engineer) es **Vagrant**, para conseguir el objetivo principal de crear sistemas de software altamente confiables y escalables. **Vagrant** es una herramienta para la creación y configuración de entornos de desarrollo virtualizados que cubre las necesidades comentadas.

Motivado por dicha relevancia a continuación se introduce la herramienta de Devops **Vagrant**.

<figure>
  <img src="./imagenes/068/001P.png"/>
</figure>

## Introducción Vagrant

### ¿Qué es Vagrant?

- Es una **herramienta** para construir entornos de desarrollo **simples**, con un flujo de trabajo fácil de usar y un enfoque en la **automatización** replicable. Permite crear y destruir los escenarios en repetidas ocasiones sin gran esfuerzo.
- Esencialmente, es una capa de software instalada entre una herramienta de virtualización (como **VirtualBox**, **Docker**, **Hyper-V**) y una máquina virtual.
- Por lo tanto, es un software para automatizar las operaciones que se realizan con los **hipervisores**:

!!! example "Por ejemplo"
    - Crear, configurar, parar, suspender y destruir máquinas virtuales.
    - Gestionar redes.
    - Compartir espacios de disco entre guest y host.
    - Manejar distintos tipos de hipervisores.

- Es un software de código libre disponible bajo una licencia `MIT`. El código fuente está disponible en [Github](https://github.com/hashicorp/vagrant).
- Fue desarrollado originalmente por **Mitchell Hashimoto** en 2010. A partir de Vagrant se creo **Hashicorp** en 2012.

[HASHICORP](https://www.hashicorp.com)

<figure>
  <img src="./imagenes/068/002P.png"/>
</figure>

!!! tip "Aplicaciones de Hashicorp"
    - **Packer** (imágenes de vagrant)
    - **Consul** (descubrimiento de servicios)
    - **Terraform** (aprovisionamiento)
    - **Vault** (seguridad)
    - **Nomad** (despliegue contenedores)

### Características

- Puede **integrarse con herramientas de gestión de la configuración**, automatizando la provisión de software de la máquina virtual.
- Forma parte del conjunto de aplicaciones utilizadas en **"infraestructura como código"**, mediante un fichero de texto que puede formar parte del código fuente del proyecto, por lo que puede subirse al control de versiones y ser compartido por todos los desarrolladores, dicho fichero es denominado **Vagrant file**.
- Soluciona problemas de incidencias de desarrollo entre diferentes sistemas.

<!-- <figure>
  <img src="./imagenes/068/004P.png" width="350"/>
</figure> -->

!!! warning "Incidencias"
    El problema de configurar escenarios a mano conlleva un trabajo tedioso y provoca errores, dando lugar al reproche de **En mi máquina funciona.**

<figure>
  <img src="./imagenes/068/005P.png" width="550"/>
</figure>

!!! abstract "Solución"
    - **Vagrant**: para Distribuir de forma separada imágenes “limpias” de OS y la configuración completa en un fichero de texto que es el que se distribuye por el grupo de trabajo.
    - Reduce el tiempo de configuración del entorno de desarrollo, aumenta la paridad de **desarrollo/producción** y hace que la excusa de **"funciona en mi máquina"** sea una reliquia del pasado.
    - A menudo se utiliza en el desarrollo de software p**ara garantizar que todos los miembros del equipo estén construyendo para la misma configuración**. 
    - No solo comparte entornos, sino que también comparte código. Esto permite que el código de un desarrollador trabaje en el sistema de otro, haciendo posible el desarrollo colaborativo y cooperativo.

## Vagrant vs. Docker

- `Vagrant` es una herramienta centrada en proporcionar un flujo de trabajo de entorno de desarrollo coherente en múltiples sistemas operativos, trabajando sobre los **hipervisores**. 
- Docker es una gestión de contenedores que puede ejecutar software de forma consistente siempre y cuando exista un sistema de contenedorización.
- Los contenedores son generalmente más ligeros que las máquinas virtuales, por lo que iniciar y detener los contenedores es extremadamente rápido. Docker utiliza la funcionalidad de contenedorización nativa en macOS, Linux y Windows. 
- Para entornos de **microservicios pesados**, Docker puede ser atractivo porque puede iniciar fácilmente una sola máquina virtual Docker e iniciar muchos contenedores por encima de eso muy rápidamente. Este es un buen caso de uso para Docker. 
- `Vagrant` también puede hacer esto con el proveedor de Docker. Un beneficio principal para Vagrant es un flujo de trabajo consistente, pero hay muchos casos en los que un flujo de trabajo de Docker puro tiene sentido.
- Tanto Vagrant como Docker tienen una vasta biblioteca de "imágenes" o "boxes" para elegir.

## Instalación Vagrant

Para la instalación el mejor consejo es acudir a la documentación oficial, en el siguiente enlace se pueden obtener los comandos de instalación de Vagrant para los diferentes tipos de sistemas operativos:

[INSTALACIÓN VAGRANT](https://developer.hashicorp.com/vagrant/downloads)

!!! note "Nota"
    En este caso se realiza la ejecución de los comandos para la instalación por repositorios en las distribuciones de Ubuntu/debian.

1. Descarga de las **GPG**: **GNU Privacy Guard** es una herramienta de cifrado y firmas digitales.

``` bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

2. Introducción de la firma en los repositorios: mediante el comando echo se añade la firma digital al `sources.list`

``` bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

3. Actualización de repositorios e instalación de Vagrant.

``` bash
sudo apt update && sudo apt install vagrant
```

4. Confirmación de la instalación

``` bash
vagrant -v
```

## Configuración del proyecto Vagrant

Para tener una máquina virtual totalmente utilizable con Ubuntu 18.04 LTS de 64 bits, solo se necesitan los siguientes dos comandos:

1. Empieza por crear un directorio para almacenar tu archivo Vagrant:

``` bash
sudo mkdir vagrant-test
```

``` bash
cd vagrant-test
```

2. Se inicializa una maquina virtual, Descargando una distribución del [VAGRANT HUB](https://app.vagrantup.com/boxes/search).

``` bash
vagrant init hashicorp/bionic64
```

!!! note "Nota"
    Este comando descarga una `Vagrantbox` y genera un `vagrantfile` en el directorio del proyecto.

!!! abstract "Vagrantbox"
    - La unidad básica en una configuración de Vagrant se llama `box` o `Vagrantbox`. Esta es una imagen completa e independiente de un entorno de sistema operativo.
    - Una `Vagrantbox` es un clon de una imagen básica del sistema operativo. El uso de un clon acelera el proceso de lanzamiento y aprovisionamiento.

!!! abstract "vagrantfile"
    Un `Vagrantfile` es un archivo de Ruby que le indica a Vagrant que cree, dependiendo de cómo se ejecute, nuevas máquinas o `Vagrantbox`.


3. Antes de poder continuar con el siguiente paso, asegúrate de que Vagrant haya creado un `Vagrantfile`.

``` bash
ls -al
```

4. Se arranca la máquina virtual.

``` bash
vagrant up
```

5. Se puede acceder por ssh a la máquina virtual.

``` bash
vagrant ssh
```

<figure>
  <img src="./imagenes/068/001G.gif"/>
  <figcaption>Creación del proyecto de Vagrant, inicialización de MV y comprobación.</figcaption>
</figure>

<!-- <figure>
  <img src="./imagenes/068/003G.gif"/>
  <figcaption>Creación de una MV configurada.</figcaption>
</figure> -->

## Configuración básica

Por defecto, Vagrant almacena las VMs ‘boxed’ en la ruta `~/.vagrant.d`. Estas ‘boxes’ suelen ser ficheros de gran tamaño, y podemos considerarlas como una ‘imagen modelo’ de las MVs que tendremos en ejecución. Se pueden compartir entre diferentes usuarios, con el objetivo de optimizar el espacio utilizado en disco.

La ubicación del directorio principal de Vagrant puede modificarse a través de la variable de entorno VAGRANT_HOME. Por ejemplo, a través del siguiente comando:

``` bash
export VAGRANT_HOME=/ruta/al/directorio
```

!!! example "Ejemplo"
    Como puedes mover tus archivos `Vagrant` y máquinas virtuales `VirtualBox` de tu carpeta de inicio a un disco duro externo:

1. Copia el directorio `.vagrant.d` de tu carpeta personal (`~/.vagrant.d`) al disco externo (Y renombra la carpeta a `vagrant_home`:

``` bash
cp -R ~/.vagrant.d /Volumes/[VOLUME_NAME]/vagrant_home"
```

2. Elimina el directorio `.vagrant.d` fde la carpeta personal:

``` bash
rm -rf ~/vagrant.d
```

3. Edita tu archivo `.bash_profile`, y añade la siguiente línea:

``` bash
export VAGRANT_HOME="/Volumes/[VOLUME_NAME]/vagrant_home"
```

- Para editarlo directamente:

``` bash
echo 'export VAGRANT_HOME="/Volumes/[VOLUME_NAME]/vagrant_home' >> ~/.bash_profile
```

!!! warning "advertencia"
    Para hacerlo permanente y no solo valga para una sesión, es necesario introducirlo en el `.bash_profile`

4. Abre VirtualBox, vaya a Preferencias y establezca la carpeta de máquina predeterminada en una ubicación en su disco duro externo (por ejemplo una nueva carpeta llamada "VMs VirtualBox").

## Crear una MV ya configurada

Hay `Boxes` subidas al Vagrant Cloud que no solo aportan un Sistema Operativo "limpio" sino que a parte tienen configurada alguna aplicación o servicio, para añadirlas correctamente su Vagrantfile viene especificado en el `git` que aparece en la descripción de la `box`.

!!! example "Ejemplo"
    [rasmus/php7dev](https://app.vagrantup.com/rasmus/boxes/php7dev)

Para realizar un uso adecuado de está caja se clona el repositorio que contiene el Vagrantfile configurado con las características de esta imagen:

``` bash
 git clone https://github.com/rlerdorf/php7dev.git
```

Una vez clonado aparece la carpeta descargada donde podemos apreciar los archivos de `Vagrantfile` y de configuración `.yml`, como se observa a continuación.

<figure>
  <img src="./imagenes/068/005G.gif"/>
  <figcaption>Vagrantfile y .yml de la Caja descargada.</figcaption>
</figure>

!!! note "Nota"
    Una vez realizado el Vagrant up se puede acceder al servicio de `PHP`

## Principales comandos Vagrant

En la [DOCUMENTACIÓN OFICIAL](https://developer.hashicorp.com/vagrant/docs/cli) se encuentran todos los comandos de Vagrant, en los siguiente apartados se realiza un resumen de los más utilizados.

!!! warning "Advertencia"
    ¡Asegúrate de estar en el mismo directorio que Vagrantfile cuando ejecutes estos comandos!

### Crear una MV

- `vagrant init`           -- Inicializa Vagrant con un `Vagrantfile` y . /.vagrant directorio, sin imagen base especificada. Antes de poder hacer vagrant up, tendrás que especificar una imagen base en el `Vagrantfile`.
- `vagrant init <boxpath>` -- Inicializa Vagrant con una caja específica. Para encontrar una caja, accede al  [public Vagrant box catalog](https://app.vagrantup.com/boxes/search). Cuando encuentres uno que te guste, reemplaza su nombre con `boxpath`. Por ejemplo, `vagrant init ubuntu/trusty64`.

### Empezar una MV

- `vagrant up`                  -- comienza el entorno de vagrant (también disposiciones solo sobre el PRIMER vagrant up).
- `vagrant resume`              -- reanudar una máquina suspendida (vagrant up también se puede utilizar).
- `vagrant provision`           -- uerza el reaprovisionamiento de la máquina de vagrant.
- `vagrant reload`              -- reinicia la máquina vagrant, carga la nueva configuración de `Vagrantfile`
- `vagrant reload --provision`  -- reinicie la máquina virtual y fuerce el aprovisionamiento

### Accediendo a una máquina Vagrant

- `vagrant ssh`           -- se conecta a la máquina a través de `SSH`
- `vagrant ssh <boxname>` -- Si le das un nombre a tu box en tu Vagrantfile, puedes acceder via `ssh` sustituyendo el boxname por el nombre. Funciona desde cualquier directorio.

### Parando una MV

- `vagrant halt`        -- detiene la máquina de vagrant.
- `vagrant suspend`     -- suspende una máquina virtual (recuerda el estado).

### limpiando una MV

- `vagrant destroy`     -- detiene y elimina todos los rastros de la máquina de Vagrant.
- `vagrant destroy -f`   -- lo mismo que lo anterior, sin confirmación.

### Boxes
- `vagrant box list`              -- ver una lista de todas las cajas instaladas en tu ordenador.
- `vagrant box add <name> <url>`  -- descarga una imagen de caja en tu ordenador.
- `vagrant box outdated`          -- comprueba si hay actualizaciones en la vagrant box.
- `vagrant box remove <name>`   -- elimina una caja de la máquina
- `vagrant package`               -- empaqueta un virtualbox env en ejecución en una caja reutilizable

### Guarda el Progreso

-`vagrant snapshot save [options] [vm-name] <name>` -- vm-name is often `default`. Permite guardar una instanténea
### Tips
- `vagrant -v`                    -- consigue la versión vagrant
- `vagrant status`                -- estado de las salidas de vagrant machine
- `vagrant global-status`         -- stado de las salidas de todas las vagrant machines
- `vagrant global-status --prune` -- igual que el anterior, pero las entradas no son válidas
- `vagrant provision --debug`     -- usa la bandera de depuración para aumentar la verbosidad de la salida
- `vagrant push`                  -- ¡sí, vagrant se puede configurar para implementar código! [deploy code](http://docs.vagrantup.com/v2/push/index.html)!
- `vagrant up --provision | tee provision.log`  -- Ejecuta `vagrant up`, fuerza el aprovisionamiento y registra toda la salida en un archivo

## Vagrantfile

!!! tip "Consejo"
    Para visualizar por consola la lista de máquina virtuales en virtual box se puede utilizar el comando: `VBoxManage list vms`

- La configuración de un escenario concreto se realiza de forma bastante simple mediante modificaciones en el `Vagrantfile`, que está escrito en formato Ruby, como ya se ha comentado.

!!! note "Nota"
    Realmente la configuración que se aplica es la aplicación en serie de varios `Vagranfiles`, tal como se explica en en la documentación oficial en [Load Order and Merging](https://developer.hashicorp.com/vagrant/docs/vagrantfile#load-order-and-merging), aunque lo más habitual es que se cargue el `Vagrantfile` que incluye el box y el que exista en el directorio de trabajo, siendo este último el que se modifica en la mayoría de los casos.

### Modificaciones de la máquina virtual

- Las modificaciones se configuran en el espacio de nombres `config.vm`, prefijo que antecede a los parámetros en este caso, `por ejemplo` para modificar el hostname de la máquina se utiliza:

``` bash
config.vm.hostname = "UbuntuServer22_Vagrant"
```

<figure>
  <img src="./imagenes/068/002G.gif"/>
  <figcaption>Generar hostname a la MV de Vagrant.</figcaption>
</figure>

!!! note "Nota"
    El resto de parámetros que se pueden modificar los encontramos en la documentación de [Vagrant: Machine Settings](https://developer.hashicorp.com/vagrant/docs/vagrantfile/machine_settings), dejando en este caso para secciones posteriores algunos de los aspectos que necesitan más desarrollo como la configuración de la red o la configuración integrada de la máquina virtual mediante shell scripts o mediante aplicaciones como `Ansible` o `Puppet`.

- Los parámetros relativos a las **características de hardware de la máquina virtual** dependen del proveedor en Vagrant y en el caso de VirtualBox se definen mediante una sub-sección desde donde se realizan las modificaciones apropiadas en un Vagrantfile. **Por ejemplo** para cambiar el nombre de la máquina virtual, la memoria RAM asignada y el número de núcleos virtuales.

``` bash
 config.vm.provider "virtualbox" do |vb|
     vb.name = "nombre"
     vb.memory = "512"
     vb.cpus = 2
   end
```

- Como la forma habitual de gestionar máquinas virtuales en `Vagrant` es mediante la línea de comandos y accediendo a ellas a través de ssh, no tiene mucho sentido que se arranque una interfaz gráfica, pero en algunas ocasiones es conveniente. Para activar la interfaz gráfica de usuario al levantar la máquina, se debe añadir la siguiente línea al `Vagrantfile`.

``` bash
config.vm.provider "virtualbox" do |vb|
     vb.gui = true     
   end
```

### Directorio compartido

Por defecto cuando se ejecuta una MV con Vagrant se crea una carpeta compartida entre el Anfitrión y la MV. en este caso:

<figure>
  <img src="./imagenes/068/006P.png"/>
  <figcaption>Directorio compartido por defecto en Vagrant.</figcaption>
</figure>

<figure>
  <img src="./imagenes/068/007P.png"/>
  <figcaption>En la MV de Vagrant se observa el Vagrantfile.</figcaption>
</figure>

!!! note "Generar archivos"
    Si se genera un archivo en esta carpeta se generaría también en el anfitrión.

!!! warning "Advertencia"
    Existen casos en los cuales la carpeta compartida no se sincroniza correctamente, para solucionarlo existen las siguientes alternativas:

- Mediante `rsync`, la cual es una aplicación libre para sistemas de tipo Unix y Microsoft Windows que ofrece transmisión eficiente de datos incrementales. Para realizar la configuración Con Vagrant haríamos:

``` bash
config.vm.synced_folder ".", "/vagrant", type: "rsync"
```

- Y lanzar en el `host` el comando:

``` bash
vagrant rsync-auto
```

!!! note "Nota"
    La sincronización se lleva a cabo desde host a guest

- Otras alternativas son `NFS` o `SMB` de forma bidireccional:

``` bash
config.vm.synced_folder ".", "/vagrant", type: "nfs"
```

!!! Warning "Advertencia"
    Se requiere un servidor NFS (o SMB) en el host => se requiere red privada

### Aprovisionamiento ligero (thin provisioning)

- Para los casos de necesidad de clonar muchas máquinas se aconseja utilizar el **Aprovisionamiento ligero (thin provisioning)**, el cual es una técnica muy utilizada en diferentes sistemas de virtualización.

- Dicha técnica consiste en crear un disco de imagen de máquina virtual que incluya sólo las modificaciones respecto a una imagen base, consiguiendo un ahorro significativo de espacio en disco a costa de una pequeña penalización en rendimiento. 

- A continuación se muestra la configuración de un Vagrantfile para que realiza el `aprovisionamiento ligero`.

1. Revisamos cuanto ocupa la MV de ejemplo de hashicorp.

<figure>
  <img src="./imagenes/068/008P.png"/>
</figure>

2. Para hacer la creación de la máquina ligera clonada se realiza en una carpeta a parte un nuevo proyecto y añadimos las siguientes líneas al `Vagrantfile`.

``` bash
config.vm.provider "virtualbox" do |vb|
     vb.name = "ligera"
     vb.linked_clone = true
   end
```

<figure>
  <img src="./imagenes/068/004G.gif"/>
  <figcaption>Configuración de aprovisionamiento ligero.</figcaption>
</figure>

### Redirección de puertos

- Si se necesita habilitar puertos para determinados servicios a instalar en la MV, se puede realizar una redirección de puertos. 

- Por ejemplo si además de utilizar la redirección de puertos de `ssh` que utiliza Vagrant se debería añadir la siguiente línea:

``` bash
config.vm.network "forwarded_port", guest: 80, host: 8080
```

!!! tip "Comprobación"
    Para comprobarlo se instalaría un NGINX en la máquina y accederíamos al servicio por el puerto configurado.

### Añadir disco adicional

Una de las configuraciones más utilizadas en los escenarios de entornos virtualizados es la adición de discos adicionales para obtener diferentes tipos de configuraciones como puede ser la redundancia de datos mediante técnicas de `RAID`.

En este caso Vagrant no dispone de comandos ni configuraciones para realizarlo, por lo que se debe añadir la programación necesaria en el `VagrantFile` que actue directamente al Virtualbox.

!!! example "Ejemplo"
    Creación de una máquina virtual que tenga un disco adicional de 500 GiB. Para ello se puede utilizar el siguiente código:

``` bash
config.vm.provider "virtualbox" do |vb|
     file_to_disk = 'tmp/disk.vdi'
     unless File.exist?(file_to_disk)
       vb.customize ['createhd', 
                     '--filename', file_to_disk, 
                     '--size', 500 * 1024]
     end
     vb.customize ['storageattach', :id, 
                   '--storagectl', 'SATAController', 
                   '--port', 1, 
                   '--device', 0, 
                   '--type', 'hdd', 
                   '--medium', file_to_disk]
   end
```

!!! warning "Advertencia"
    - Este tipo de configuraciones en las que se pone de forma explícita las características de la máquina virtual no son ni mucho menos generales, en el caso anterior se está poniendo de forma concreta el puerto `SATA` al que conectar el disco y el nombre del controlador `SATA`, características que pueden variar de una máquina virtual a otra. 
    - Por lo tanto Es recomendable inicialmente comprobar el nombre del controlador de `SATA` que en el caso de VirtualBox se puede hacer con el siguiente comando de VBoxManage

``` bash
VBoxManage showvminfo NOMBRE_MV
```

<figure>
  <img src="./imagenes/068/005G.gif"/>
  <figcaption>Comprobación del nombre del controlador de SATA y creación del disco adicional.</figcaption>
</figure>

- Después de levantar el Vagrant o realizar un `reload`, se comprueba la configuración:

``` bash
lsblk
ls -hl tmp/
```

<figure>
  <img src="./imagenes/068/006G.gif"/>
  <figcaption>Comprobación de la creación del disco adicional.</figcaption>
</figure>

## Gestión de instantáneas

- Para generar y recuperar instantáneas en `Vagrant` se utiliza el comando `vagrant snapshot` seguido de la acción a realizar o bien guardar o recuperar.

!!! example "Ejemplo"
    Partiendo de un escenario de vagrant con una MV que tenga instalada un NGINX y un reenvío de puertos. Se genera una `snapshot` para realizar algún cambio y restaurar la captura guardada.

- Comandos:

``` bash
# se genera la captura.
vagrant snapshot save nginx-limpio

# se comprueba que se ha creado.
vagrant snapshot list

# Después de realizar algún cambio se restaura.
vagrant snapshot restore nginx-limpio
```

!!! tip "Consejo"
    Existe un método más sencillo de realizar instantáneas, es mediante el comando `vagrant snapshot push` que va almacenando instantáneas cada vez que se invoca y se recupera la más reciente con `vagrant snapshot pop`.

## Redes

- Por defecto Vagrant realiza los pasos necesarios en la máquina virtual para que sea accesible a través de una red virtual desde el equipo anfitrión y tenga acceso a Internet, es decir le configura una red.

- Esta configuración lógicamente va a variar en función del proveedor, pero en el caso de VirtualBox se trata de una red de tipo NAT con el direccionamiento inicial 10.0.2.0/24. Las opciones de redes son:

### Red privada

En algunas ocasiones aparece la necesidad de utilizar un direccionamiento IP específico en una máquina virtual o añadir una red virtual adicional, por lo que en muchos casos es adecuado añadir una red privada, tal como se explica en [Private Networks](https://developer.hashicorp.com/vagrant/docs/networking/private_network).

!!! example "Ejemplo"

``` bash
config.vm.network "private_network", ip: "192.168.55.10"
```

### Red pública

Para vagrant una red pública es una red en modo puente (**bridged network**) conectada al exterior, típicamente sería poner en la misma red la máquina anfitriona y la máquina virtual. Más detalles en [Public Networks](https://developer.hashicorp.com/vagrant/docs/networking/public_network)

!!! example "Ejemplo"

``` bash
config.vm.network  "public_network", bridge: "eth0"
```

## Multi-máquinas

- Se puede extender la configuración de un escenario virtual con vagrant a aquellos casos en los que haya más de una máquina virtual y esto se hace con estructuras como la siguiente en el Vagrantfile:

``` bash
Vagrant.configure("2") do |config|
  config.vm.define "Server" do |m1|
    m1.vm.box = "Ecodev/ubuntu-server-2204"
  end
  config.vm.define "Client" do |m2|
    m2.vm.box = "hashicorp/bionic64"
  end
end
```

- Al existir más de una máquina virtual, muchas de las instrucciones que hemos visto hasta ahora deben incluir el nombre de la máquina como parámetro, por ejemplo si hiciéramos:

``` bash
vagrant ssh Server
```

!!! Example "Ejemplo"
    Crea un escenario para una aplicación web en la que ubicamos en una máquina el servidor **Ubuntu 22** conectado a una red pública exterior y una máquina con un **cliente** en una máquina no accesible desde el exterior. Para que ambas máquinas estén interconectadas entre sí, crea la red privada `10.0.100.0/24` a la que estarán conectadas ambas máquinas.

``` bash
Vagrant.configure("2") do |config|
     config.vm.define "ServerMulti1" do |nodo1|
       nodo1.vm.box = "Ecodev/ubuntu-server-2204"
       nodo1.vm.hostname = "UbuntuServerMulti"
       nodo1.vm.network "public_network", bridge: "en0"
       nodo1.vm.network "private_network", ip: "10.0.100.101"
     end
     config.vm.define "ClientMulti1" do |nodo2|
       nodo2.vm.box = "hashicorp/bionic64"
       nodo2.vm.hostname = "ClienteMulti"
       nodo2.vm.network "private_network", ip: "10.0.100.102"
     end
   end
```

## Actividades de clase (AP)

### AC64. Introducción a entornos de desarrollo mediante Vagrant 

Realiza los siguientes tareas introductorias a **Vagrant**.

1. Monta el escenario necesario para trabajar con `Vagrant`.

- Instala `Vagrant`.
- Realiza un primer proyecto.
- Externaliza la carpeta de boxes a un disco externo (si es posible).
- Crea y destruye varias MVs.

2. Genera un Vagrantfile multimáquina con un servidor web y un cliente que pueda acceder al servidor de forma privada. El servidor debe tener salida a Internet. Comprueba que tiene acceso al `index.html` del servidor web y modifícalo.

3. Crea un entorno de desarrollo portable con Vagrant con algunas de las boxes más utilizadas del catálogo (por ejemplo ubuntu/xenial64 o centos7):

- Prueba los distintos tipos de `synced folders`. Comprueba si, para tu plataforma, en cada uno de ellos funciona adecuadamente los enlaces simbólicos y la sincronización en uno y otro sentido:
- Shared folder de virtualbox ▹ Rsync
- NFS
- Prueba a compartir por los métodos anteriores carpetas de tu equipo en otras ubicaciones de la máquina guest.

!!! note "NOTA" 
    usa vagrant reload cada vez que cambies la configuración.

4. Crea una máquina con un NGINX y un reenvío de puertos, Genera una `Snapshot` haz algún cambio en el `index.html`. Regenera la captura y comprueba que el index.html está como de inicio.