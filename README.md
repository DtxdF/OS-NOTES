# Notas de sistemas operativos

Este proyecto está enfoncado en preservar información, que generalmente no está presente en la documentación oficial, o, en algunos casos, no está detallada como para resolver un problema muy específico.

Se es libre de documentar cualquier sistema operativo. Se es libre de colocar cualquier nota, pero es preferible crear una que no esté oficialmente documentada o de escasos detalles, ya que sería hacer doble-trabajo.

Las notas también pueden ser un buen punto de partida para luego aportar oficialmente a un proyecto (p.ej.: una traducción).

## Formato

No deseo volver complejo el proyecto, ya que es innecesario hacerlo complejo, pero es requerido seguir cierto formato para controlar el caos.

### Nombre de los sistemas operativos

Las notas de los sistemas operativos se colocarán en [OS/](OS/), siguiendo una subcarpeta con el nombre del sistema operativo (p. ej.: OS/FreeBSD). Los nombres de los sistemas operativos deben ser alfanuméricos, minúsculas o mayúsculas, se permiten guiones y no deben contener espacios (**[a-zA-Z0-9]**). Los espacios deben sustituirse por guiones bajos (**_**). Es preferible el nombre de un sistema operativo como es llamado oficialmente, por ejemplo, *Arch Linux* es preferible a *arch linux*.

Una lista de ejemplos de cómo debería ir cada nota de los sistemas operativos:

* OS/FreeBSD
* OS/OpenBSD
* OS/Arch_Linux
* OS/antiX_Linux
* OS/NetBSD
* OS/Debian

En el caso de que se quiera documentar un software que es válido por todos o la mayoría de sistemas operativos conocidos, se pueden usar un nombre especial **ALL** para describir esa pieza:

* OS/ALL

En el caso de que se quiera documentar un software que es válido por una familia de sistemas operativos o que usen un mismo kernel, se puede usar esa familia como el nombre:

* OS/Linux
* OS/BSD

### Formato de las notas

El formato de las notas es muy simple: se usará solamente Markdown y todo lo que sea válido referente a él.

### Nombre de las notas

El nombre de las notas sigue las mismas reglas que el del sistema operativo: los espacios no son válidos, por ejemplo, aunque esta regla es más relajada aquí, ya que en vez de usar guiones bajos, también es posible usar guiones como sustitutos de los espacios.

Los nombres de las notas siguen la siguiente especificación: \<**name**\>.\<**id**\>, donde \<**name**\> es el nombre de la nota y debe seguir las reglas descritas anteriormente. El \<**id**\>, es un número que se aumenta progresivamente, de 001 a 999 por cada nota que se va agregando.

Una lista de ejemplos de cómo debería ir cada nota:

* OS/FreeBSD/jail-dhcp.001
* OS/FreeBSD/pkg-tips-and-tricks.002
* OS/OpenBSD/ifstated-tutorial.001
* OS/FreeBSD/devd-tutorial.003
* OS/FreeBSD/jail-tutorial.004
* OS/FreeBSD/containers.005
* OS/FreeBSD/zfs.006
* OS/Arch_Linux/tricks.001
* OS/Linux/docker.001
* OS/ALL/DHCPD-and-BIND9.001
* OS/ALL/zfs.002

### Escritura

No es requerida una escritura formal, es posible siguiendo un estilo personal de cada quien. Lo único que es recomendable es que sea legible. No es necesario ser puristas en la ortografía, pero debe ser entendible para el lector sin casi esfuerzo implicado.

\~ DtxdF