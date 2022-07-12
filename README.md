# Notas de sistemas operativos

Este proyecto está enfoncado en preservar información, que generalmente no está presente en la documentación oficial, o, en algunos casos, no está detallada como para resolver un problema muy específico.

Se es libre de documentar cualquier sistema operativo. Se es libre de colocar cualquier nota, pero es preferible crear una que no esté oficialmente documentada o de escasos detalles, ya que sería hacer doble-trabajo.

Las notas también pueden ser un buen punto de partida para luego aportar oficialmente a un proyecto (p.ej.: una traducción).

Si solo se desea leer las notas, es posible comenzar desde [os-notes/](os-notes/index.md).

## Formato

No deseo volver complejo el proyecto, ya que es innecesario hacerlo complejo, pero es requerido seguir cierto formato para controlar el caos.

### Nombre de los sistemas operativos

Las notas de los sistemas operativos se colocarán en [os-notes/](os-notes/), siguiendo una subcarpeta con el nombre del sistema operativo (p. ej.: os-notes/freebsd). Los nombres de los sistemas operativos deben ser alfanuméricos y en minúsculas. Se permiten guiones y no deben contener espacios. Los espacios deben sustituirse por guiones bajos (**_**).

Una lista de ejemplos de cómo debería ir cada nota de los sistemas operativos:

* os-notes/freebsd
* os-notes/openbsd
* os-notes/arch_linux
* os-notes/antix_linux
* os-notes/netbsd
* os-notes/debian

En el caso de que se quiera documentar un software que es válido por todos o la mayoría de sistemas operativos conocidos, se puede usar un nombre especial **all** para describir esa pieza:

* os-notes/all

En el caso de que se quiera documentar un software que es válido por una familia de sistemas operativos o que usen un mismo kernel, se puede usar esa familia como el nombre:

* os-notes/linux
* os-notes/bsd

**Todas** las carpetas deben contener un `index.md` como índice, como por ejemplo:

* os-notes/index.md
* os-notes/freebsd/index.md
* os-notes/freebsd/howto/index.md
* os-notes/freebsd/howto/system/index.md
* os-notes/freebsd/howto/system/devd-001/index.md

El último, para este ejemplo, es el contenido de la nota.

### Formato de las notas

El formato de las notas es muy simple: se usará solamente Markdown y todo lo que sea válido referente a él.

### Nombre de las notas

El nombre de las notas sigue las mismas reglas que las del sistema operativo: los espacios no son válidos, por ejemplo, aunque esta regla es más relajada aquí, ya que en vez de usar guiones bajos, también es posible usar guiones como sustitutos de los espacios.

Las notas deben ir dentro un tipo de nota. Los tipos de notas deben ir dentro de una categoría. Las categorías dentro de un sistema operativo. Ver la sección "**Categorías y tipos de notas**".

Los nombres de las notas siguen la siguiente especificación: \<**name**\>-\<**id**\>, donde \<**name**\> es el nombre de la nota y debe seguir las reglas descritas anteriormente. El \<**id**\>, es un número que se aumenta progresivamente, de 001 a 999 por cada nota que se va agregando. Cada nota es una carpeta, que debe contener por lo menos un archivo, y ese debe ser nombrado `index.md`.

Una lista de ejemplos de cómo debería ir cada nota:

* os-notes/freebsd/tips/security/jail-dhcp-001/index.md
* os-notes/freebsd/tips/system/pkg-tips-and-tricks-001/index.md
* os-notes/freebsd/howto/system/devd-tutorial-001/index.md
* os-notes/freebsd/howto/security/jail-tutorial-001/index.md
* os-notes/freebsd/howto/containers/pot-001/index.md
* os-notes/freebsd/howto/containers/bastillebsd-002/index.md
* os-notes/freebsd/howto/filesystem/zfs-001/index.md
* os-notes/openbsd/howto/networking/ifstated-001/index.md
* os-notes/arch_linux/howto/system//tricks-001/index.md
* os-notes/linux/howto/containers/docker-001/index.md
* os-notes/all/howto/networking/dhcpd-and-bind9-001/index.md
* os-notes/all/howto/filesystem/zfs-001/index.md

### Categorías y tipos de notas

#### Tipos de notas

Los tipos de notas definen para qué contexto es el tipo de herramienta que se está usando. No necesariamente es el contexto del problema. Por ejemplo, las jaulas en FreeBSD se usan para mejorar la seguridad en la mayoría de casos, pero puede que se esté describiendo un tip acerca de cómo usarlas con DHCP (mirar FreeBSD/tips/security/jail-dchp-001).

* networking: Relacionado con redes;
* security: Relacionado con la seguridad;
* containers: Relacionado con los contenedores;
* filesystem: Relacionado con los sistemas de archivos;
* system: Relacionado con utilidades del sistema o para la administración del sistema.

#### Categorías

* howto: Un *howto* o tutorial provee información detallada acerca de una herramienta;
* tips: Son consejos o sugerencias que sirven como aclaratoria de un asunto o para resolver un problema determinado.

### Escritura

No es requerida una escritura formal, es posible siguiendo un estilo personal de cada quien. Lo único que es recomendable es que sea legible. No es necesario ser puristas en la ortografía, pero debe ser entendible para el lector sin casi esfuerzo implicado.

### Cómo contribuir

Si deseas contribuir, puedes enviar un *pull request* con una nota o corrección de una nota. También puedes enviarla a través de correo eléctronico usando hacia mi dirección [DtxdF-pm@pm.me](mailto:DtxdF-pm@me.pm)

Si deseas, puedes unirte a la comunidad de [Los Locos de FreeBSD](https://t.me/ellocodebsd) en Telegram. Ahí también aceptaremos más notas.

### Colaboradores

* Jesús Daniel Colmenares Oviedo ([DtxdF](https://github.com/DtxdF))
* Alonso Cárdenas ([jacardenasm](https://github.com/jacardenasm))
