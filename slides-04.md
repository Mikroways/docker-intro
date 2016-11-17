***
# Volúmenes
---
## ¿Cómo guardo la información?

* Los contenedores son volátiles e inmutables.
* Debemos preservar la información importante.
* ¿Dónde?
  * En volúmenes de datos.

---
## Características de los volúmenes

* No utilizan un sistema de archivos de unión (UFS).
* Pueden compartirse y reusarse entre contenedores.
* Los cambios se hacen directamente en el volumen.
* La información del volumen **no se incluye** en la imagen.
* Persisten aún cuando se eliminen todos los contenedores que los usan.
  * Pueden quedar volúmenes sin referenciar.

---
## Tipos de volúmenes

* Volúmenes anónimos.
* Volúmenes nombrados.
* Volúmenes desde el SO host.

---
## Tipos de volúmenes

* Al crear un volúmen anónimo o nombrado, la información que exista en el punto
  de montaje se copia al volumen.
* Con volúmenes desde el SO host o desde otro contenedor, se oculta la
  información que exista en el punto de montaje.
  * Correspondencia con el comando mount.

---
## Volúmenes anónimos

```bash
$ docker volume ls
  DRIVER    VOLUME NAME

$ docker run -it -v /opt ubuntu /bin/bash
  root@a9c1a6e6c0ea:/# ls /opt/
  root@a9c1a6e6c0ea:/# echo "Prueba" > /opt/archivo
  root@a9c1a6e6c0ea:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  a9c1a6e6c0ea  ubuntu   "/bin/bash"   2 minutes ago  Exited (0) About a minute ago  small_jennings

$ docker volume ls
  DRIVER    VOLUME NAME
  local     e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f
```

---
## Volúmenes anónimos

```bash
$ docker volume inspect e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f
  [
      {
          "Name": "e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f",
          "Driver": "local",
          "Mountpoint": "/var/lib/docker/volumes/e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f/_data",
          "Labels": null,
          "Scope": "local"
      }
  ]

$ ls /var/lib/docker/volumes/e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f/_data
  archivo

$ cat /var/lib/docker/volumes/e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f/_data/archivo
  Prueba
```

---
## Volúmenes anónimos

```bash
$ docker run -it -v /opt ubuntu /bin/bash
  root@cad2a83c1c50:/# ls /opt/
  root@cad2a83c1c50:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  cad2a83c1c50  ubuntu   "/bin/bash"   2 minutes ago  Exited (0) About a minute ago  big_almeida
  a9c1a6e6c0ea  ubuntu   "/bin/bash"   11 minutes ago Exited (0) 10 minutes ago      small_jennings

$ docker volume ls
  DRIVER    VOLUME NAME
  local     483ea67555fb592d25e51fe513b42f4a611398ad2824c029d7767a605eb7967d
  local     e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f
```

---
## Volúmenes nombrados

```bash
$ docker volume ls
  DRIVER    VOLUME NAME

$ docker run -it -v test:/opt ubuntu /bin/bash
  root@7def6f99f957:/# ls /opt/
  root@7def6f99f957:/# echo "Prueba" > /opt/archivo
  root@7def6f99f957:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  7def6f99f957  ubuntu   "/bin/bash"   2 minutes ago  Exited (0) 2 minutes ago       mad_mccarthy

$ docker volume ls
  DRIVER    VOLUME NAME
  local     test
```

---
## Volúmenes nombrados

```bash
$ docker volume inspect test
  [
      {
          "Name": "test",
          "Driver": "local",
          "Mountpoint": "/var/lib/docker/volumes/test/_data",
          "Labels": null,
          "Scope": "local"
      }
  ]

$ ls /var/lib/docker/volumes/test/_data
  archivo

$ cat /var/lib/docker/volumes/test/_data/archivo
  Prueba
```

---
## Volúmenes nombrados

```bash
$ docker run -it -v test:/opt ubuntu /bin/bash
  root@2899fc8bc061:/# ls /opt/
  archivo
  root@2899fc8bc061:/# cat /opt/archivo
  Prueba
  root@2899fc8bc061:/# exit

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  2899fc8bc061  ubuntu   "/bin/bash"   14 seconds ago Exited (0) 6 seconds ago       prickly_nobel
  7def6f99f957  ubuntu   "/bin/bash"   5 minutes ago  Exited (0) 4 minutes ago       mad_mccarthy

$ docker volume ls
  DRIVER    VOLUME NAME
  local     test
```

---
## Volúmenes desde el SO host

Verificamos que no existe ningún volumen de Docker.

```bash
$ docker volume ls
  DRIVER    VOLUME NAME
```

Creamos un directorio en el sistema operativo host.

```bash
$ mkdir /data
$ ls /data
```

Ejecutamos el contenedor montando el directorio creado.

```bash
$ docker run -it -v /data:/opt ubuntu /bin/bash
```

---
## Volúmenes desde el SO host

Dentro del contenedor, vemos que nada existe en /opt.

```bash
root@5e09b8264bf0:/# ls /opt/
```
Desde el host, creamos un archivo con contenido.

```bash
$ echo "Prueba" > /data/archivo
```

Verificamos /opt en el contenedor y agregamos contenido.

```bash
  root@5e09b8264bf0:/# ls /opt/
  archivo
  root@5e09b8264bf0:/# echo "Otra prueba" >> /opt/archivo
```

Finalmente, en el host, vemos el archivo actualizado.

```bash
$ cat /data/archivo
Prueba
Otra prueba
```

---
## Volúmenes desde el SO host

Ahora, chequeamos los volúmenes de Docker creados.

```bash
$ docker volume ls
  DRIVER    VOLUME NAME
```

* Cuando se usan volúmenes montados desde el host, no se crea ningún volumen de
Docker.
  * Igual lógica que al montar un recurso en un equipo Linux.

---
## Volúmenes desde otro contenedor

* Me puede interesar montar los volúmenes que tiene otro contenedor.

```bash
$ docker run -d -v /opt --name ubuntu-pruebas ubuntu /bin/bash -c 'echo "Prueba" > /opt/archivo'
fe9e2cddd9c3f37bb01e322978e0ed6008b005dea86ed31bee47da57e5e35863

$ docker ps -a
  CONTAINER ID  IMAGE    COMMAND       CREATED        STATUS                         NAMES
  fe9e2cddd9c3  ubuntu   /bin/bash -c  2 minutes ago  Exited (0) 2 minutes ago       ubuntu-pruebas

$ docker run -it --volumes-from ubuntu-pruebas ubuntu /bin/bash
  root@555ec4005dcd:/# ls /opt/
  archivo
  root@555ec4005dcd:/# cat /opt/archivo
  Prueba
```

---
## Volúmenes desde otro contenedor

Ejemplo de uso: backups.

Creamos un directorio en el host para backups.

```bash
$ mkdir /data
$ ls /data
```

Copiamos los datos a un volumen en el host.

```bash
$ docker run --rm --volumes-from ubuntu-pruebas -v /data:/backups ubuntu /bin/bash -c 'cp -a /opt/* /backups'
```

Al finalizar la ejecución, tenemos los datos en el host.

```bash
$ ls /data
  archivo
$ cat /data/archivo
  Prueba
```
***
