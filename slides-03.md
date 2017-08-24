***
# Consideraciones para trabajar con docker
---
## Introducción

* Ya sabemos que:
  * Las imágenes Docker son inmutables.
  * Los contenedores crean una capa con las diferencias correspondientes
    respecto de la imagen original.
* Entonces los contenedores deberían minimizar los cambios respecto de la imagen
  original.
  * Optimizando el uso de espacio y evitando impactos de performance.
  * Promoviendo la reusabilidad.

---
## Inmutabilidad en la infraestructura

* Desplegar una actualización de una aplicación, consiste en crear nuevas
  intancias y destruir las anteriores, en vez de actualizarlas sobre la
  instancia productiva.
* Una vez que una aplicación está corriendo, **¡evitamos tocarla!** promoviendo
  así:
  * Repetibilidad.
  * Reducir costos de mantenimiento.
  * Simplificar rollbacks.

---
## Inmutabilidad en la infraestructura

* Para lograr este tipo de inmutabilidad deben cumplirse los siguientes
  requerimientos:
  * La aplicación debe ser stateless. Su estado debe almacenarse en un servicio
    por fuera del alcance de la *infraestructura inmutable*.
  * Existe un template y/o conjunto de instrucciones que permiten desplegar una
    instancia de la aplicación desde cero.
* El segundo punto lo resuelve fácilmente Docker.

---
## ¿Qué es dinámico entonces?

* La creación de las imágenes debe conocer bien el dominio para identificar las
  partes que son dinámicas:
  * Archivos que se generan por la aplicación.
  * Uploads desde la aplicación.
  * Logs.
  * Spool.

---
## ¿Cómo verificar si mis contenedores crecen?

Un mal diseño de las imágenes impactará en la performance de los contenedores
que generarán grandes capas con datos dinámicos.

Ante la actualización del contenedor, estos datos se perderán.

---
## ¿Cómo verificar si mis contenedores crecen?

```bash
$ docker run -it ubuntu:16.04 /bin/bash
root@6ce39ac62830:/#
```

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        0B (virtual 120MB)
```

---
## ¿Cómo verificar su mis contenedores crecen?

```bash
root@6ce39ac62830:/# echo "hola" > /tmp/prueba
```

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        5B (virtual 120MB)
```

---
## ¿Cómo verificar si mis contenedores crecen?

<small>
El tamaño es lo que crece el contenedor respecto de la imagen. El tamaño virtual
es lo que ocupa el contenedor sumado al tamaño de la imagen.
</small>

```bash
root@6ce39ac62830:/# dd if=/dev/zero of=/tmp/lala.img bs=1M count=10
10+0 records in
10+0 records out
10485760 bytes (10 MB, 10 MiB) copied, 0.0127865 s, 820 MB/s
```

```bash
$ docker ps -s

docker ps -s
CONTAINER ID        IMAGE          ....        SIZE
6ce39ac62830        ubuntu:16.04   ....        10.5MB (virtual 131MB)
```

---
## ¿Cómo verificar si mis contenedores crecen?

Es posible ver en los contenedores qué archivos cambiaron.

```
$ docker diff 6ce39ac62830
C /tmp
A /tmp/lala.img
A /tmp/prueba
```

---
## Buenas prácticas

* **Los contenedores deben ser efímeros:** pararlos, destruirlos y volverlos 
  a iniciar con una mínima configuración.
* **Evitar paquetes innecesarios:** las imágenes no deben incluir paquetes que
  no se utilicen.
* **Un proceso por contenedor:** en la mayoría de los casos, se debe correr un
  proceso por contenedor. Desacoplar aplicaciones en múltiples contenedores
  hace mucho más simple el escalamiento horizontal y reuso de contenedores.
* **La (in)necesidad de ssh**: acceder a un contenedor es algo que debemos
  evitar. En términos de infraestructura inmutable, el servicio no debería
  considerar SSH.
***

