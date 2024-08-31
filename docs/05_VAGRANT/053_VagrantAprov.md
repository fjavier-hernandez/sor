--- 
title: Aprovisionamiento Vagrant
description: Apuntes de Aprovisionamiento de Vagrant del módulo Sistemas Operativos en red (SOR) del docente Francisco Javier Hernández Illán. 
---

# Aprovisionamiento Vagrant

En escenarios con un numero elevado de máquinas, por ejemplo los servidores y clientes en un IES, los cuales se cuentan como decenas de equipos, la tarea de configuración de servicios en dichos equipos se convierte en tediosa. Para solucionar este inconveniente existen **herramientas de la gestión de la configuración** (configuration management systems) como `puppet`, `chef` o `ansible`.

El campo de las **herramientas de la gestión de la configuración** es muy amplio y su explicación detallada sale del ámbito de este módulo, por lo que simplemente se realiza una breve introducción al respecto. En el caso de vagrant la solución que se ha encontrado es muy razonable, ya que en lugar de desarrollar una nueva herramienta `Hashicorp` ha optado por una solución más adecuada y razonable: integrarlas todas con vagrant.

En este caso se va a trabajar el "**aprovisionamiento**" con `scripts` y `ansible`.

!!! note "**Nota**" 
    Para realizar el aprovisionamiento `Ansible` se necesita profundizar en esta plataforma cuyo estudio queda fuera de competencia de este módulo, por lo tanto a continuación se explica el aprovisionamiento con `scripts` de bash.

## Aprovisionamiento con Scripts

A continuación se muestran unos breves ejemplos de aprovisionamiento de MVs con Scripts.

El objetivo es realizar un entorno de `Vagrant` sencillo en el que tras arrancar una máquina se realiza una actualización de la lista de paquetes y se efectúan las actualizaciones de aquellos paquetes necesarios mediante apt-get.

!!! example "**Ejemplo 1 Actualizar Sistema Operativo**"

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

!!! example "**Ejemplo 2 Instalar Docker**"

 si queremos instalar Docker en la imagen virtual como ya vimos en un post anterior Instalar Docker Ubuntu Podríamos añadir lo siguiente:

``` ruby
  config.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      apt-get update
      apt-get install -y docker-ce
		
      #Añada al usuario al grupo docker.
      sudo usermod -aG docker $USER	
  SHELL
```

## PT52. Automatización de servicios de recursos compartidos

!!! info "Contribución a los CEs (Criterios de Evaluación)"
    Estas actividades contribuyen a los criterios de evaluación **CE1, CE2, CE3, CE6 y CE7** del **RA4** de SOR.

!!! Abstract "Situación de Aprendizaje"
    Tras las primeras pruebas con **Vagrant** te das cuenta de su gran potencial y decides compartir la experiencia con tus compañeros a los cuales propones la siguiente prueba de laboratorio.

Realiza un proyecto por grupos de 2 para realizar una exposición del siguiente punto:

1. Despliega un sistema heterogéneo con **Vagrant**, para a continuación aprovisionar el sistema generado con un servicio **Samba-DC** mediante **"scripts"**. En la exposición se deben adjuntar las comprobaciones necesarias del correcto funcionamiento del servicio.

!!! note "Nota"
    los sistemas heterogéneos a elegir son los mismos que la práctica PT51.