***
# Docker
## Introducción

---
## Antecedentes

* Antiguamente, transportar bienes tenía muchos problemas.
  * Diferentes tamaños, formas, resistencias, etc.
  * Capacidad de transporte reducida.
  * Difícil realizar un seguimiento.
  * Pérdida parcial de mercadería.
  * Grandes costos.

---
## Contenedores

* Los contenedores solucionaron muchos problemas:
  * Un vendedor pone todos sus productos en un contenedor y sólo debe
    preocuparse por ese contenedor.
  * Los productos nunca se manipulan individualmente.
  * Tamaños y formas estandarizadas, simplifica toda la cadena de transporte: el
    transporte sólo debe llevar contenedores.

---
## Contenedores

<img alt="Contenedores tradicionales" src="images/traditional-containers.jpg" height="480px" />

---
## ¿Qué es docker?

* Contenedores de software.
  * Empaqueta aplicaciones en una unidad estándar de intercambio.
* Única pieza de software en un filesystem completo que contiene **todo lo
  necesario** para ejecutar una aplicación: código, librerías, herramientas,
  etc.
* Garantiza que el software **siempre correrá de igual forma** sin importar su
  ambiente.

---
## ¿Por qué Docker?

* Diferencias entre el ambiente de desarrollo, testing y producción.
* Instalación de una aplicación en diferentes plataformas.
* Deploy de aplicaciones complejas.
* Ejecución de código antiguo.
* Simplicidad para escalar horizontalmente.

---
## Matriz del infierno

<img alt="Matriz del infierno sin Docker" src="images/matrix-of-hell-wo-docker.jpg" height="480px" />

---
## Matriz del infierno

<img alt="Matriz del infierno con Docker" src="images/matrix-of-hell.jpg" height="480px" />

---
## Comparación con máquinas virtuales

<table>
<tr><td>
<img alt="Máquina virtual" src="images/wid-vm.png" height="300px" />
</td><td>
<img alt="Contenedor" src="images/wid-container.png" height="300px" />
</td></tr>
</table>

---
## Historia

* Emerge como proyecto de SL en 2013.
* Virtualización a nivel de sistema operativo.
* Se basa en el uso de:
  * [Cgroups](https://en.wikipedia.org/wiki/Cgroups) para restringir recursos
    como cpu, memoria, IO, red, etc.
  * [Kernel namespaces](https://en.wikipedia.org/wiki/Linux_namespaces) permite
    aislar y virtualizar recursos de una colección de procesos como por ejemplo:
    PID, hostname, UID, acceso a la red, comunicación entre procesos,
    filesystem, etc.
  * [Filesystem de unión](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/)
    como es el caso de AUFS, OverlayFS, Btrfs, Device Mapper, ZFS, etc.

---
## Historia

* Con las características antes mencionadas se obtienen contenedores
  independientes en una instancia Linux que evita el overhead de manipular VMs.
* Antes de la versión 0.9, Docker usaba LXC como base. A partir de la 0.9
  incorporaron libcontainer, eliminando la dependencia de LXC dado que accede
  directamente al kernel para manipular cgroups, namespaces, apparmor, interfaces
  de red, etc.


---
## Imágenes y contenedores

* Imagen:
  * Filesystem y parámetros para utilizarla.
  * No cambia nunca y no tiene estados.
* Contenedor:
  * Instancia de una imagen (resultado de ejecutarla).
  * Tiene una capa de RW volátil.

---
## Imágenes y contenedores

<img alt="Imagen de Docker" src="images/image-layers.jpg" height="400px" />

---
## Imágenes y contenedores

<img alt="Contenedor de Docker" src="images/container-layers.jpg" height="400px" />

---
## Imágenes y contenedores

<img alt="Compartiendo una imagen" src="images/sharing-layers.jpg" height="480px" />

---
## Imágenes y contenedores

<img alt="Imagen derivada" src="images/saving-space.png" height="400px" />

---
## Instalación de Docker

* Docker puede instalarse en:
  * Linux.
  * MacOS.
  * Windows.

---
## Instalación de Docker en Linux

* Requisitos:
  * Sistema de 64 bits.
  * Kernel 3.10 o superior.
* Existen binarios para la mayoría de las distribuciones.

---
## Instalación de Docker en Windows/MacOS

* Usando Docker Toolbox.
  * Utiliza Docker Machine (no nativo).
  * Windows 7/MacOS 10.8 o superior
* Docker for (Windows/Mac):
  * Corre una aplicación nativa usando (Hyper-V/xhyve para virtualizar la Docker
    Engine).
  * Windows 10/MacOS 10.10.3 o superior.

---
## Comandos básicos

```bash
# Más usados
docker run
docker ps
docker build
docker images
docker logs
docker inspect
docker volume

# Otros comandos comunes
docker commit
docker pull
docker push
docker tag
```

---
## Nuestro primer contenedor

```bash
$ docker run -it ubuntu:16.04 /bin/bash
  Unable to find image 'ubuntu:16.04' locally
  16.04: Pulling from library/ubuntu
  
  6bbedd9b76a4: Pull complete
  fc19d60a83f1: Pull complete
  de413bb911fd: Pull complete
  2879a7ad3144: Pull complete
  668604fde02e: Pull complete
  Digest: sha256:2d44ae143feeb36f4c898d32ed2ab2dffeb3a573d2d8928646dfc9cb7deb1315
  Status: Downloaded newer image for ubuntu:16.04
  
  root@99a3403db59a:/# cat /etc/issue
  Ubuntu 16.04.1 LTS \n \l
```

---
## Dockerfile

* Archivo de texto plano para crear imágenes de Docker.
* Permite escribir instrucciones a ejecutar.
* Automatiza el proceso de la creación de imágenes.
* Permite repetir y modificar fácilmente una imagen.
* Generar de forma simple imágenes derivadas.

---
## Dockerfile

```bash
FROM ubuntu:16.04
MAINTAINER Leandro Di Tommaso

# Instalar Nginx y configurar una página personalizada
RUN apt-get update && apt-get install -y nginx
RUN mkdir /var/www/html/ejemplo
RUN echo "<html><h1>Nginx en Docker</h1></html>" > /var/www/html/ejemplo/index.html

EXPOSE 80
CMD    ["nginx", "-g", "daemon off;"]
```

---
## Nuestra primer imagen

```bash
$ docker build -t leoditommaso/nginx:1.0.0 .
  Sending build context to Docker daemon 2.048 kB
  Step 1 : FROM ubuntu:16.04
  16.04: Pulling from library/ubuntu
  6bbedd9b76a4: Already exists
  fc19d60a83f1: Already exists
  de413bb911fd: Already exists
  2879a7ad3144: Already exists
  668604fde02e: Already exists
  Digest: sha256:2d44ae143feeb36f4c898d32ed2ab2dffeb3a573d2d8928646dfc9cb7deb1315
  Status: Downloaded newer image for ubuntu:16.04
   ---> f753707788c5
  Step 2 : MAINTAINER Leandro Di Tommaso
   ---> Running in f93e6923c21e
   ---> a1144bb80b28
  Removing intermediate container f93e6923c21e
  Step 3 : RUN apt-get update && apt-get install -y nginx
   ---> Running in 489697f5e5d5
  Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]

  ... (SALIDA REMOVIDA)

  Processing triggers for systemd (229-4ubuntu10) ...
   ---> 136943551ea1
  Removing intermediate container 489697f5e5d5
  Step 4 : RUN mkdir /var/www/html/ejemplo
   ---> Running in 443cfc08af15
   ---> aff42d6aa899
  Removing intermediate container 443cfc08af15
  Step 5 : RUN echo "<html><h1>Nginx en Docker</h1></html>" >
  /var/www/html/ejemplo/index.html
   ---> Running in 2bea67c34185
   ---> 4c9ad433769b
  Removing intermediate container 2bea67c34185
  Step 6 : EXPOSE 80
   ---> Running in c3bc2fd14bff
   ---> df1abe4570d5
  Removing intermediate container c3bc2fd14bff
  Step 7 : CMD nginx -g daemon off;
   ---> Running in 9d39a9f69468
   ---> 8fd110f3364a
  Removing intermediate container 9d39a9f69468
  Successfully built 8fd110f3364a

$ docker images
  REPOSITORY          TAG    IMAGE ID       CREATED          SIZE
  leoditommaso/nginx  1.0.0  8fd110f3364a   2 minutes ago    223.2 MB
  ubuntu              16.04  f753707788c5   4 weeks ago      127.2 MB
```

---
## Nuestra primer imagen

```bash
$ docker run -d -p 80:80 leoditommaso/nginx:1.0.0
```

<img alt="Nginx con Docker personalizado" src="images/docker-nginx-custom.png" height="300px" />

---
## La registry

* Servicio para almacenar y distribuir imágenes de Docker.
* Open source (Licencia Apache).
* Instalación privada
  * Acceso local para mayor velocidad de descarga.
  * Imágenes en un ambiente controlado y gestionado por la organización.
* Servicio en la nube (Docker Hub).
  * Libre de mantenimiento.

---
## Docker Hub

* Gratis para imágenes públicas.
* Soporta builds automáticos (desde Github/Bitbucket).
* Cuentas para organizaciones.
* Plan pago para imágenes privadas.
* https://hub.docker.com
***
