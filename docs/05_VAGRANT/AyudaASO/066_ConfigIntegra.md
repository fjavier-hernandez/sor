# Configuración Integrada de Sistemas Heterogéneos

## Justificación

En escenarios con un numero elevado de máquinas, por ejemplo los servidores y clientes en un IES, los cuales se cuentan como decenas de equipos, la tarea de configuración de servicios en dichos equipos se convierte en tediosa. Para solucionar este inconveniente existen **herramientas de la gestión de la configuración** (configuration management systems) como `puppet`, `chef` o `ansible`.

El campo de las **herramientas de la gestión de la configuración** es muy amplio y su explicación detallada sale del ámbito de este módulo, por lo que simplemente se realiza una breve introducción al respecto. En el caso de vagrant la solución que se ha encontrado es muy razonable, ya que en lugar de desarrollar una nueva herramienta `Hashicorp` ha optado por una solución más adecuada y razonable: integrarlas todas con vagrant.

En este caso se va a trabajar el "**aprovisionamiento**" con scripts y `ansible`.

<figure>
  <img src="./imagenes/069/001P.png"/>
</figure>

## Introducción Ansible

### ¿Qué es Ansible?

[DOCUMENTACIÓN OFICIAL](https://docs.ansible.com/ansible/latest/)

- Tecnología de software libre que nos permite aprovisionar sistemas.
- Mejoras significativas respecto a utilizar Shell scripts.
- Amplia gama de módulos para aprovisionar servidores.
- Herramienta declarativa. (NGINX installed)
- Sin necesidad de configurar agentes. SSH

!!! note "NOTA"
    - Con la arquitectura basada en agentes, los nodos deben instalar localmente un proceso de comunicaciones con la máquina de control. 
    - Con la arquitectura sin agentes los nodos no necesitan instalar ni ejecutar en segundo plano ningún proceso que se comunique con la máquina de control. 
    - Este tipo de arquitectura reduce la sobrecarga de la red y previene el uso de estrategias de control más agresivas por parte del servidor (como puede ser la realización de sondeos, con sus constantes operaciones de consulta).

## Características

- **Aprovisionamiento** -> Con Ansible se pueden aprovisionar las últimas plataformas en la nube, host virtualizados e hipervisores, dispositivos de red y servidores físicos.

- **Gestión de la configuración** -> Establece y mantiene el rendimiento de los paquetes de software instalados y las ubicaciones y direcciones de red de los dispositivos de hardware.

- **Despliegue de aplicaciones** -> Cuando se define la aplicación con Ansible y se maneja su despliegue con Ansible Tower es posible llevar un control de todo el ciclo de vida de una aplicación. Desde desarrollo hasta producción.

- **Seguridad y Cumplimiento** -> Ansible permite definir las seguridad en los sistemas de forma sencilla. Utilizando la sintaxis de un Playbook es posible definir reglas de firewall, gestión de usuarios y grupos y políticas de seguridad personalizadas en los sistemas que se estén gestionando y además posees un gran número de módulos que ayudan en la labor.

- **Orquestación** -> Ansible se usa para orquestar los despliegues de OpenStack por ejemplo. Compañías como Rackspace, CSC, HP, Cisco e IBM confían en Ansible para mantener sus nubes OpenStack disponibles de manera simple y segura.

!!! nota "Nota"
    - OpenStack: es un proyecto de computación en la **nube** para proporcionar una infraestructura como servicio (IaaS). Es un software libre y de código abierto. Es decir, Cloud Computing Open Source.

<figure>
  <img src="./imagenes/069/002P.png"/>
</figure>

## Instalación

- Para tener conexión a los servidores, es recomendable generar claves (pública y privada) y copiar la pública a los servidores que queremos configurar.
- Pasos instalación -> [INSTALACIÓN](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu)

``` bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible -y
```

## Arquitectura Ansible

Hay dos tipos de máquinas necesarias en la arquitectura Ansible:

- Nodo del controlador 
- Hosts gestionados.

En la siguiente imagen se puede apreciar un esquema de la arquitectura de `Ansible`:

<figure>
  <img src="./imagenes/069/003P.png"/>
  <figcaption>Arquitectura Ansible.</figcaption>
</figure>

- Los elementos principales de la arquitectura de Ansible son:

## Controlador

- El nodo del controlador es la máquina principal en la que se instala ansible. Todos los `Playbooks` se ejecutan desde un nodo de controlador. 
- El nodo del controlador es el jefe de la arquitectura `ansible`.

## Inventory

- Todos los hosts administrados que estamos utilizando en el entorno ansible deben aparecer en un solo archivo simple, junto con las direcciones IP, ssh_password, ssh_user_name y muchos otros atributos de los hosts administrados. 
- Ese archivo simple se conoce como Inventario en Ansible.
- La dirección del archivo de inventario debe darse en el archivo de configuración ansible. 
- El inventario puede ser dinámico y estático.

!!! example "Ejemplo"

``` yaml
---
[webservers]
www1.example.com
www2.example.com

[dbservers]
db0.example.com
db1.example.com
```

## Playbooks

- Los "manuales técnicos" son archivos de texto simples escritos en el lenguaje `YAML` que consisten en una serie de ordenes de reproducción.
- Los Playbooks permiten realizar una serie de tareas en el orden en que se han escrito. 
- Además en ellos se almacenan el conjunto de instrucciones que se le dan al ansible para realizar una funcionalidad en particular.
- Un `Playbook` define el flujo de trabajo en una arquitectura ansible.

!!! note "Nota"
    `YAML` significa "YAML Ain't Markup Language" es un lenguaje de serialización de datos que es un lenguaje fácil de usar y se utiliza en la escritura de archivos JSON y playbooks.

!!! example "Ejemplo"

``` yaml
---
- hosts: webservers
  serial: 5 # update 5 machines at a time
  roles:
  - common
  - webapp

- hosts: content_servers
  roles:
  - common
  - content

```


## Module

- `Ansible` funciona conectándose a sus nodos y enviando scripts llamados "Módulos Ansibles" a ellos.
- `Ansible` ejecuta estos módulos (a través de SSH por defecto) y los elimina cuando termina.
- La biblioteca de módulos puede residir en cualquier máquina, y no se requieren servidores, demonios o bases de datos.
 
!!! note "Nota" 
    En ansible, hay cientos de módulos que se utilizan para realizar una amplia gama de tareas de automatización. También podemos escribir nuestros propios módulos.

!!! example "Ejemplo"
    Cómo conectarse a dispositivos RouterOS con la API de RouterOS

``` yaml
---
- name: RouterOS test with API
  hosts: localhost
  gather_facts: false
  vars:
    hostname: 192.168.1.1
    username: admin
    password: test1234
  tasks:
    - name: Get "ip address print"
      community.routeros.api:
        hostname: "{{ hostname }}"
        password: "{{ password }}"
        username: "{{ username }}"
        path: "ip address"
        # The following options configure TLS/SSL.
        # Depending on your setup, these options need different values:
        tls: true
        validate_certs: true
        validate_cert_hostname: true
        # If you are using your own PKI, specify the path to your CA certificate here:
        # ca_path: /path/to/ca-certificate.pem
      register: print_path

    - name: Show IP address of first interface
      ansible.builtin.debug:
        msg: "{{ print_path.msg[0].address }}"
```

## Plugins

- Los complementos aumentan la funcionalidad principal de Ansible. 
- Mientras que los módulos se ejecutan en el sistema de destino en procesos separados (generalmente eso significa en un sistema remoto), los complementos se ejecutan en el nodo de control dentro del proceso `/usr/bin/ansible`. 
- Los complementos ofrecen opciones y extensiones para las características principales de Ansible: transformación de datos, registro de la salida, conexión al inventario y más.

## Aprovisionamiento

A continuación se muestran unos breves ejemplos de aprovisionamiento de MVs con Scripts y `Ansible`.

### Vagrant con Scripts

El objetivo es realizar un entorno de `Vagrant` sencillo en el que tras arrancar una máquina se realiza una actualización de la lista de paquetes y se efectúan las actualizaciones de aquellos paquetes necesarios mediante apt-get.

1. Se crea el fichero `actualizar.sh` ubicado en el mismo directorio que el Vagrantfile y con el siguiente contenido:

``` bash
#!/bin/bash
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get -y upgrade
```

2. Se crea el siguiente Vagrant file.

``` ruby
Vagrant.configure("2") do |config|
  config.vm.box = "Ecodev/ubuntu-server-2204"
  config.vm.provision "shell", path: "actualizar.sh"
end
```

### Vagrant con Ansible

Partiendo del mismo escenario que el anterior caso, pero ahora utilizando ansible, que debe estar instalado en la máquina anfitriona.

1. Se crea un fichero `site.yml` de configuración de ansible para que se realice la actualización de paquetes:

``` yaml
---
- name: actualizar
  hosts: all
  become: true
  tasks:
    - name: Ensure system is updated
      apt: update_cache=yes upgrade=yes
```

2. El Vagrant file para aplicar la configuración sería:

``` ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "Ecodev/ubuntu-server-2204"
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"
  end
end
```

<figure>
  <img src="./imagenes/069/004P.png"/>
  <figcaption>Actualización Vagrant con Ansible.</figcaption>
</figure>

## Actividades de profundización (AP)

### PR62. Automatización de servicios de recursos compartidos

Realiza un proyecto por grupos de 3 para realizar una exposición en clase del siguiente punto:

1. Despliega un sistema heterogéneo con Vagrant, para a continuación aprovisionar, el sistema generado, un servicio **Samba-DC**. Se podrá elegir entre aprovisionamiento con **"scripts"** o con **Ansible**. En la exposición se deben adjuntar las comprobaciones necesarias del correcto funcionamiento del servicio.

!!! note "Nota"
    los sistemas heterogéneos a elegir son los mismos que el proyecto PR61.

<!-- 2. Crea un cluster de 3 máquinas con Vagrant con la siguiente configuración:

- Nodo1: Servidor Ubuntu
- Nodo2: Windows 10
- Nodo3: Fedora

Aprovisiona mediante `Ansible` el servicio Samba-DC al servidor y a los clientes, comprueba que se pueden loguear. -->

<!-- 3. El siguiente es un ejemplo de configuración integrada de Vagrant con ansible, donde se despliega un clúster de balanceo de carga mediante un `round-robin` en un servidor `DNS`.

Clona el siguiente repositorio y sigue las instrucciones que se incluyen en el fichero README.md:

https://github.com/albertomolina/ejemplo-ansible-vagrant -->


