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
* El segundo punto lo resuelve fácilmente docker

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

### El siguiente comando permite verificar esto

```bash
$ docker ps -s

CONTAINER ID        IMAGE     .....               SIZE
0d5c12033ee3        nginx     .....    2 B (virtual 182.8 MB)

```
<small>
El tamaño es lo que crece el contenedor respecto de la imagen. El tamaño virtual
es lo que ocupa el contenedor sumado al tamaño de la imagen.
</small>

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

