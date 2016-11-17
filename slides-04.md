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
## Analizando los volúmenes

Al iniciar un contenedor, la opción -v permite indicar qué volumen utilizar. El
siguiente ejemplo define tres volúmenes: uno anónimo, uno nombrado y uno desde
el SO host:

```bash
$ docker run -it \
      -v /opt \                       # anonimo
      -v test-volume:/test-volume \   # nombrado
      -v /tmp:/tmp \                  # SO host
      ubuntu bash
```

Inspeccionando los volúmenes vemos:

```bash
$ docker volume ls
  DRIVER    VOLUME NAME
  local     e9c7022b8c7bec55891ca44b8c40de1e5f41cf0fe9505a334bca06a484a5ff1f
  local     test-volume
```

***
