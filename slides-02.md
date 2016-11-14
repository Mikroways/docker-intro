***
# Docker
## Introducción
---
## Historia

* Emerge como proyecto de SL en 2013
* Virtualización a nivel SO: Linux
  * Y a partir de 2016, Windows
* Se basa en el uso de:
  * [cgroups](https://en.wikipedia.org/wiki/Cgroups) para restringir recursos como cpu, memoria, io, red, etc
  * [kernel namespaces](https://en.wikipedia.org/wiki/Linux_namespaces) permite
    aislar y virtualizar recursos de una colección de procesos como por ejemplo:
    PID, hostname, UID, acceso a la red, comunicación entre procesos,
    filesystem, etc
  * [Filesystem con características de unión](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)
    como es el caso de AUFS, OverlayFS, Btrfs, Device Mapper, etc
---
## Historia
* Con las características antes mencionadas se obtienen contenedores independientes en una
  instancia Linux que evita el overhead de manipular VMs
* Antes de la versión 0.9, Docker usaba LXC como base, pero en esta versión, a
  través de la incorporación de libcontainer eliminaron la dependencia de LXC y
libcontainer accede directamente al kernel para manipular cgroups, namespaces,
apparmor, interfaces de red, etc
---
## ¿Qué es docker?

* Empaqueta aplicaciones en una unidad estándar de intercambio
  * Los contenedores docker engloban una pieza de software en un filesystem
    completo que contiene **todo lo necesario** para correr el código: código,
    runtime, herramientas de sistema, librerías, etc
* Esto garantiza que el software **siempre correrá de igual forma** sin importar
  su ambiente
---
## Características de los contenedores

* Livianos
* Abiertos
* Seguros
---
## Imágenes y contenedores
* Ver
  https://michael.bouvy.net/blog/en/2014/02/01/what-is-docker-and-how-to-use-it-lxc-openvz/
* http://www.infoworld.com/article/3077875/linux/containers-101-docker-fundamentals.html
---
Instalando docker
---
La registry
---
Nuestro primer contenedor
---
Ejemplos de uso con docker:
php
ruby
nginx
apache
varnish
eclipse (o algo gráfico)
***
