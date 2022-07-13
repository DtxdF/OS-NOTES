# Cómo montar un sistema de archivos ext2/ext3/ext4, XFS y BTRFS

A veces es necesario, por nuestro propio diseño en el esquema de particionado, y por el compañerismo de otros sistemas de archivos no propios de FreeBSD, como lo pueden ser los mencionados en el título, que montar tales sistemas de archivos compliquen un poco más las cosas para ciertas tareas, pero por suerte contamos con paquetes especializados que nos harán la vida más fácil.

Para lograr montar los sistemas de archivos antes mencionados, es necesario instalar los siguientes paquetes, con el siguiente comando:

```sh
pkg install e2fsprogs e2fsprogs-libblkid fusefs-ext2 fusefs-lkl
```

El primer paquete nos otorgará las herramientas mkfs.ext2, mkfs.ext3 y mkfs.ext4, las cuales estarán a nuestra disposición para crear los susodichos sistemas de archivos. El segundo paquete nos brinda la utilidad blkid. El tercer paquete nos da la posibilidad de montar sistemas de archivos ext2, ext3 y ext4. Por último, y siendo éste el cuarto paquete: fusefs-lkl, que nos da la posibilidad de montar los sistemas de archivos BTRFS, ext4, y XFS.

La diferencia principal entre el penúltimo y el último paquete en montar ext4, es que, además de que el último tenga la posibilidad de montar otros sistemas de archivos muy utilizados, también el sistema ext4 es más estable para la escritura.

Ya instalado, podemos listar los discos y particiones junto con los sistemas de archivos instalados en ellos.

```
blkid
```

Suponiendo que tengamos una partición ext4 que está en /dev/da0s1, entonces podríamos ejecutar lo siguiente:

```sh
lklfuse -o type=ext4 /dev/da0s1 /mnt
ls /mnt
umount /mnt
```

Ahora podríamos también, si quisiéramos, crear en /dev/da0s1 un sistema de archivos ext4, usando el siguiente comando:

```
mkfs.ext4 /dev/da0s1
```

Véase también:

* lklfuse --help
* mke2fs(8)
* blkid(8)

\~ DtxdF