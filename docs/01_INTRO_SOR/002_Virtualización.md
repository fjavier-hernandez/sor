--- 
title: Virtualización/Contenedores
description: Explicación de la virtualización, hipervisor y sus ventajas por Javier Hernández Illán. Explicación de contenedores comparándolos con la virtualización
---

# Virtualización

En general, el objetivo de la virtualización es poder utilizar **simultáneamente** en un mismo ordenador dos o más sistemas operativos. Por lo tanto, para poder hablar de virtualización tienen que estar **funcionando a la vez varios sistemas operativos**.

!!! note "Nota"
    * De acuerdo con esta definición, instalar dos sistemas operativos en un ordenador (**Windows y Linux**, por ejemplo) y poder elegir uno u otro mediante un arranque dual **no se considera virtualización**, puesto que mediante un arranque dual no podemos ejecutar a la vez ambos sistemas.
    * Y tampoco sería virtualización la simulación, que consiste en simular el aspecto visual del sistema imitado. Por ejemplo, podríamos instalar un tema de escritorio en **GNOME** o **KDE** que imitara el escritorio de Windows. El problema de esta simulación sería que realmente no estaríamos utilizando Windows sino simplemente algo que parece Windows. Así que, por ejemplo, no podríamos instalar una aplicación de Windows puesto que el sistema operativo sería Linux, que no acepta instaladores de Windows.

!!! tip "A tener en cuenta"
    * Los **sistemas operativos** son los encargados de la **gestión del hardware y requieren un control completo** del mismo, por lo que dos sistemas operativos **no pueden en principio estar funcionando a la vez** sobre el mismo hardware.
    * La solución para la **virtualización** es la existencia de un **hipervisor** (en inglés, hypervisor).

Antes de definir que es el hipervisor, es importante conocer las ventajas que aporta la virtualización.

## Ventajas Virtualización

La virtualización tiene muchas aplicaciones interesantes:

* La más habitual es **poder ejecutar aplicaciones que no están disponibles para el sistema operativo host (anfitrión)**, pero sí para otro sistema operativo que se instalaría como guest (huésped).
* La virtualización permite **la conservación del software**. Debido al progreso del hardware, los procesadores antiguos dejan de fabricarse y los sistemas operativos y aplicaciones antiguos dejan de desarrollarse y dejan de funcionar en el hardware moderno. Pero si el sistema operativo puede instalarse como guest, puede seguir utilizándose para siempre.
* La virtualización **permite depurar y comprobar el funcionamiento de los programas y los sistemas operativos**. Si un programa provoca un fallo de funcionamiento total, si se está ejecutando como guest, el sistema host puede recoger información sobre el motivo del fallo.
* La virtualización permite un mejor **aprovechamiento del hardware**, ya que un mismo ordenador puede contener muchos sistemas guests utilizados por usuarios diferentes, aislados unos de otros.

## Hipervisor

!!! tip "Definición"
    Un **hipervisor**, conocido también como **monitor de máquinas virtuales**, es un proceso que crea y ejecuta máquinas virtuales. Un hipervisor permite que un ordenador host preste soporte a varias máquinas virtuales invitadas **mediante el uso compartido virtual de sus recursos, como la memoria y el procesamiento**.

Por lo tanto **el hipervisor es la pieza fundamental de la virtualización**. Tradicionalmente, se distinguen dos tipos de hipervisores:

* Los hipervisores de **tipo 1**, denominados «***hipervisores bare metal***», dichos hipervisores están en contacto directo con el hardware de la máquina, sin necesidad de ningún sistema operativo previo.
* Los hipervisores de **tipo 2**, denominados «***alojados***», y son una aplicación más del sistema operativo instalado en la máquina y el hipervisor accede al hardware de la máquina a través de ese sistema operativo.

<figure>
  <img src="imagenes/HipervisorTipo.png"/>
  <figcaption>Tipos de Hipervisor.</figcaption>
</figure>

!!! note "Nota"
    * En el caso de los hipervisores de **tipo 2**, el sistema operativo que tiene el control del hardware recibe el nombre de host (anfitrión). Es el sistema operativo que se instaló primero en el ordenador y el que se pone en marcha al arrancar el ordenador. Los demás sistemas operativos reciben el nombre de guests (huéspedes) y pueden ejecutarse o no a voluntad del usuario.
    * En el caso de los hipervisores de **tipo 1** no hay un sistema operativo host, todos los sistemas operativos son guests del hipervisor.

!!! Example "Ejemplo"
    * **Tipo 1**: ejemplos de este tipo de hipervisores son *VMware ESXi* o *Xen*, los cuales son utilizados a nivel de centro de datos. **Hyper-V** de Microsoft se puede considerar un hipervisor de ***tipo 1***, porque aunque necesita que esté instalado primero Windows para poder instalar *Hyper-V*, una vez instalado convierte a Windows en un guest más de **Hyper-V** (aunque con acceso prioritario con respecto al resto de guests). 
    * **Tipo 1**/**Tipo 2**: *KVM*, al estar integrado en el kernel de Linux, se puede considerar tanto de ***tipo 1*** como de ***tipo 2***. 
    * **Tipo 2**: *VirtualBox* es un hipervisor de ***tipo 2***.

## Contenedores

El problema de los hipervisores y las máquinas virtuales es que cada máquina virtual es independiente de las demás. Al no reutilizarse ningún componente, se ocupa **mucho espacio tanto en disco como en memoria** y el **tiempo de ejecución siempre será mayor** que si sólo hubiera un sistema operativo (sobre todo en el caso de **hipervisores de tipo 2**).

Para resolver este problema se crearon los **contenedores** en los que se **utilizan mecanismos existentes en el sistema operativo para aislar las aplicaciones**, pero compartiendo el mayor número posible de componentes del sistema operativo o incluso de las aplicaciones.

* Como definición, **Un contenedor** es el equivalente a una máquina virtual de la virtualización clásica, pero mucho **más ligera porque utiliza recursos del sistema operativo del hos**t. 
* Las aplicaciones de cada contenedor "ven" un sistema operativo, que puede ser diferente en cada contenedor, pero quien **realiza el trabajo es el sistema operativo común que hay por debajo**.

!!! Note "Nota"
    * **Los contenedores** suelen ser elementos **efímeros**. La facilidad con la que pueden crearse y ponerse en marcha hace más fácil crear un nuevo contenedor que modificar uno ya existente. Por ello, los datos generados por las aplicaciones no se suelen guardar en los contenedores, sino fuera de ellos. 
    * Su ligereza hace más fácil tener varios contenedores con una aplicación en cada uno de ellos que tener un único contenedor con varias aplicaciones en él. Por ello, un **aspecto importante de los contenedores es su orquestación**, es decir, la administración simultánea de muchos contenedores, una de las herramientas más utilizadas es **[Kubernetes](https://kubernetes.io/es/)**.

## Máquinas Virtuales VS Contenedores

<figure>
  <img src="imagenes/ContenedoresVsMaquinaV.png"/>
  <figcaption>Contenedores Vs Máquinas Virtuales.</figcaption>
</figure>

### Microservicios

Uno de los objetivos principales de la configuración e implantación de contenedores es solucionar los problemas de:

* **Errores de dependencias** entre diferentes Sistemas Operativos de los trabajadores y máquinas de puesta en marcha.
* Evitar la **Elevada carga y capacidad** de las Máquinas Virtuales.
* Caída de todos los servicios instalados de forma monolítica en los servidores.

En este último punto cabe destacar la **evolución de implantación de sistemas operativos** en los Centros de Procesado de Datos:

* **SOA (Service Oriented Architecture)** → Diferentes Máquinas una con cada Servicio conectadas.
* **Monolítico** → Todos los servicios en la misma máquina.
* **MicroServicios** → División más pequeña de los servicios.

<figure>
    <img src="imagenes/Microservicios.png"/>
    <figcaption>Monolítico Vs Microservicios.</figcaption>
</figure>

### Características principales de los Contenedores

En resumen los contenedores:

* Consisten en **Agrupar y Aislar Aplicaciones o grupos de aplicaciones que se ejecutan sobre un mismo núcleo de sistema operativo**.
* Su característica principal se basa en su **propio sistema de archivos ejecutable en cualquier Sistema Operativo**.
* No es necesario emular el *HW* y *SW* completo como en las máquinas virtuales, por lo tanto son mucho más **ligeros**, comparten el máximo de componentes con el sistema operativo host, y su rapidez, ya que gracias a que apenas añaden capas adicionales consiguen casi velocidades nativas.
* Soluciona problemas de **espacio y compatibilidades a la hora de puesta en marcha** en servidores de producción.
* Los contenedores suelen ser elementos **efímeros**.
