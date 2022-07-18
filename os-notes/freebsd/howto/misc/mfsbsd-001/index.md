mfsBSD es un conjunto de scripts que generan una imagen booteable, un archivo ISO o simplemente archivos de booteo, con la finalidad de crear una instalación mínima y personalizada de FreeBSD la cual es ejecutada completamente en memoria.

<center>![](https://i.imgur.com/llQKg5g.png)</center>

mfsBSD es una excelente alternativa a la imagen de FreeBSD completa, no solo por el poco espacio en almacenamiento, no solo porque se ejecuta en la memoria, sino por su alta personalización. mfsBSD nos permite personalizar una imagen sin necesidad de compilarla, aunque también permite hacerlo por si alguien lo desea. Mayormente cuando vemos proyectos de código abierto/software libre derivados tenemos dos problemas frecuentes: o no está actualizado con la rama principal del proyecto, o su principal fuente de atracción se reduce a una perdida de funcionalidad y herramientas imperdonables.

mfsBSD no es así. Si deseas crear una imagen con ciertos paquetes pre-compilados, mfsBSD puede hacerlo por ti. Si deseas hacer un despliegue de múltiples servidores, pero eres tan perezoso que no deseas ejecutar [bsdinstall(8)](https://www.freebsd.org/cgi/man.cgi?query=bsdinstall&apropos=0&sektion=0&manpath=FreeBSD+13.1-RELEASE+and+Ports&arch=default&format=html), mfsBSD puede hacerlo por ti. Si deseas tener los archivos base como kernel.txz o base.txz en un servidor web o FTP dentro de tu misma red local para ahorrar ancho de banda descargándolo desde la página web oficial, mfsBSD puede hacerlo por ti. Si deseas tener un sistema que solo correrá en memoria con herramientas de diagnostico y reparación, mfsBSD puede hacerlo por ti.

El límite es la imaginación de la persona, y como son sólo un conjunto de scripts escritos en [sh](https://www.freebsd.org/cgi/man.cgi?query=sh&apropos=0&sektion=0&manpath=FreeBSD+13.1-RELEASE+and+Ports&arch=default&format=html) o archivos [Makefile](https://www.freebsd.org/cgi/man.cgi?query=make&apropos=0&sektion=0&manpath=FreeBSD+13.1-RELEASE+and+Ports&arch=default&format=html), su estudio, personalización, y si deseas, contribución, es realmente sencilla.

# Motivación

Algunos proveedores VPS más respetables y bien conocidos del mercado tienen una forma de reparar el sistema que se está ejecutando actualmente, o incluso nos permiten subir nuestra propia imagen de cualquier sistema operativo que deseemos. Mayormente solo soportan alguna distribución del pingüino y carecen de soporte para FreeBSD. Si tu proveedor te permite de alguna forma reparar tu sistema operativo, o si puedes sobreescribir los datos del mismo disco duro, mfsBSD puede instalarse remotamente de forma sencilla.

Aunque como comentaba en el prefacio, mfsBSD puede ser ideal para realizar un despliegue de múltiples servidores. Una vez hecho el inventario y habiendo conocido las características de cada uno, o mejor aún, si comparten las mismas características, es realmente sencillo automatizar la instalación de FreeBSD.

Con un poco de esfuerzo puedes correr mfsBSD en un entorno PXE.

# Preparación

mfsBSD se puede obtener de dos formas oficialmente. La primera es su repositorio, el cual es el más ideal para la mayoría de situaciones donde tengamos que colocar mfsBSD en producción, ya que es el modo que nos permite personalizar, o incluso si se desea, aportar a él. La segunda, la imagen pre-creada, se puede obtener desde el sitio web oficial de mfsBSD, y es más adecuado para probar mfsBSD por primera vez y queremos ver más o menos lo que nos puede ofrecer. Por supuesto, esto lo digo por lo que considero ideal, puedes usar la opción que mejor se adapte a ti según tus necesidades.

## Obteniendo mfsBSD desde su repositorio

El creador [Martin Matuska](https://github.com/mmatuska) nos permite clonar su repositorio usando git o una herramienta similar desde el mismo repositorio donde se aloja el código fuente.

```bash
git clone https://github.com/mmatuska/mfsbsd.git
```
Si esta opción encaja en tus necesidades, puedes ir directamente a la personalización e instalación en la sección siguiente.

## Obteniendo mfsBSD desde su web oficial

Esta opción es más sencilla, y como se mencionó, la más ideal para fines de prueba. Toda la información oficial y necesaria de mfsBSD puede ser obtenida desde su [web oficial](https://mfsbsd.vx.sk).

Al ingresar en la web oficial tenemos varios opciones que se explican por sí solas.

<center>![](https://i.imgur.com/2mi1EaV.png)</center>

Los archivos .ISO son ideales para llevarlos con un CD/DVD o un dispositivo capaz de leerlo e interpretarlo para correrlo en el host que vayamos a usar mfsBSD. Por otro lado, si vamos a grabar mfsBSD en un USB, necesitamos una imagen del disco duro en bruto, que son los .IMG.

Los .IMG se encuentran en la siguiente ubicación: **https://mfsbsd.vx.sk/files/images/13/amd64**

<center>![](https://i.imgur.com/cp8jIWu.png)</center>

Se puede observar que cada archivo tiene un sufijo, que es básicamente la edición de mfsBSD. La edición sin un sufijo, es mfsBSD sin nada especial más que un par de programas que todas las ediciones tienen, lo cuales son: **cpdup**, **dmidecode**, **ipmitool**, **nano**, **rsync**, **smartmontools**, **tmux**; varios módulos, que serían los siguientes: **acpi** (pre-cargado), **ahci** (pre-cargado), **aesni**, **crypto**, **cryptodev**, **ext2fs**, **geom_eli**, **geom_mirror**, **geom_nopi**, **ipmi**, **ntfs**, **nullfs**, **opensolaris**, **smbusi**, **snp**, **tmpfs**, **zfs**; y por último también tiene ajustado, al igual que todas las ediciones, *autodhcp*, que permite configurar DHCP en cualquier interfaz excepto la interfaz loopback. La diferencia se nota es cuando analizamos la edición -mini y -SE. La edición -mini es la que menos almacenamiento ocupa y como cualidad única, usa dropbear para implementar un servidor SSH. La versión -SE, o *Special Edition*, es como la edición normal pero incluye los archivo base.txz y kernel.txz, requeridos para instalar FreeBSD.

La elección de uno u otro dependerá de nuestras necesidades. La edición especial es útil para cuando no deseemos enviar los archivos a través de la red, ya sea que la red esté congestionada, o no deseamos implementar un servidor web, ftp o tampoco sintamos que tenemos la necesidad de usar SSH (con SCP o SFTP), por lo que está edición nos permite instalar FreeBSD sin necesidad de descargar nada.

La versión mini es cuando queremos ahorrar espacio y tal vez tengamos un servidor para descargar los archivos requeridos para proseguir con la instalación de FreeBSD.

# Creación de la imagen

En la anterior sección hemos preparado lo necesario para poder operar con mfsBSD. Por supuesto, si la imagen pre-creada ha sido la elección es más sencillo proseguir, pero lo que motiva este artículo a seguir no es esa sino su contraparte: la personalización.

## Grabar la imagen pre-creada

La herramienta [dd](https://www.freebsd.org/cgi/man.cgi?query=dd&apropos=0&sektion=0&manpath=FreeBSD+13.1-RELEASE+and+Ports&arch=default&format=html) nos permite escribir directamente en el disco duro la imagen pre-creada. Sus detalles se pueden apreciar en el manual, mientras que aquí se verá su modo más sencillo posible.

```bash
dd if=mfsbsd-se-13.1-RELEASE-amd64.img of=/dev/da0 bs=30m status=progress && fsync mfsbsd-se-13.1-RELEASE-amd64.img
```

Básicamente hemos escrito la imagen pre-creada de mfsBSD, edición especial porque deseo que incluya los archivos base.txz y kernel.txz, necesarios para instalar sin conexión. El dispositivo **/dev/da0** es lo que mi sistema indica, y se puede apreciar ejecutando el comando *geom disk list*. Es importante ser cuidadoso con el dispositivo de salida ya que *dd* no lo hará por nosotros. Los demás parámetros, *bs*, el tamaño del bloque, lo defino en 30 Mebibytes, y *status* con el valor en *progress*, para que me imprima el estado de a escritura hasta que concluya la operación.

Uso *fsync* para que se encargue de vaciar la caché tan rápido, ya que si hay una falla energética y no se ha vaciado, la escritura pudo quedar incompleta.

## Crear una imagen personalizada

El artículo pese a no excluir la imagen pre-creada, hace hincapié a la creación de una imagen personalizada que se adapte mejor a nuestras necesidades. Antes de proseguir, es necesario saber antes los requisitos para la creación y su uso.

### Requisitos

* En el caso de compilar, es indispensable contar con FreeBSD 11 o mayor. Lo mismo aplica a los archivos de distribución: base.txz y kernel.txz;
* En el caso de su ejecución, es necesario tener, como mínimo, 512 MiB de memoria.

### Creación de la imagen

Para generar la imagen primero necesitamos dirijirnos al directorio de trabajo, o en otras palabras, al repositorio que clonamos en la anterior sección usando el comando ***cd ./mfsbsd***. Segundo, necesitamos los archivos de distribución base.txz y kernel.txz, que se pueden obtener del directorio FTP público de FreeBSD, el cual es explicado más abajo. Si contamos con los archivos, ahora debemos pasar a la configuración.

### Descargar los archivos de distribución

Usando el comando FTP que viene incluido en FreeBSD nos da la posibilidad de obtener los archivos de distribución, como se muestra abajo.


```bash
# Estamos en el directorio de trabajo, pero es necesario tener estos archivos en una carpeta aparte
mkdir freebsd-dist
ftp -o freebsd-dist/kernel.txz ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/13.1-RELEASE/kernel.txz
ftp -o freebsd-dist/base.txz ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/13.1-RELEASE/base.txz
```

### Configuración de mfsBSD

En el directorio de trabajo hay, más o menos, una docena de archivos con directorios, pero en los que nos enfocaremos serán en los directorios **conf/** y **tools/**. El directorio **conf/** es el encargado de ajustar algunos archivos que modificaran el comportamiento de mfsBSD en ejecución, tales como configurar las interfaces de red, configurar la contraseña del usuario root para acceder remotamente o, si no deseamos o no podemos permitirnos tal inseguridad, podríamos usar nuestra clave pública SSH. Una mejor lista de lo que se encarga este directorio podría ser la que se puede apreciar más abajo.

* *authorized_keys*: usado para incluir las clave públicas SSH de authorización;
* *boot.config*: parámetros que serán usados en [boot](https://www.freebsd.org/cgi/man.cgi?query=boot&apropos=0&sektion=0&manpath=FreeBSD+13.1-RELEASE+and+Ports&arch=default&format=html);
* *hosts*: el archivo /etc/hosts para definir nombres de host de forma estática;
* *interfaces.conf*: cuando se desconoce la interfaz de red, pero se sabe la dirección MAC, es posible usar este archivo para poder configurarla;
* *loader.conf*: la configuración del arranque del sistema, aunque en mfsBSD este archivo tiene un papel adicional, ya que centraliza parte de los archivos antes mencionados y los siguientes; 
* *rc.conf*: el archivo /etc/rc.conf para iniciar o configurar algunos *daemons* del sistema, entre otras cosas;
* *rc.local*: el archivo /etc/rc.local para ejecutar comandos al inicio del sistemas;
* *resolv.conf*: el archivo /etc/resolv.conf para la configuración de los *nameservers*;
* *ttys*: la configuración de la terminal.

He explicado brevemente el significado de cada uno porque ya se conocen desde el mismo FreeBSD, e incluso cada uno tiene su propio manual rico en información, aunque hay algunos que tienen un uso especial para mfsBSD por lo que a lo largo del artículo serán descritos con mayor detalle según se conveniente.

Anteriormente mencioné que, aparte del directorio **conf/**, había otro directorio en particular que formaba parte de mfsBSD y sus scripts este es **tools/**, que contiene scripts que son usados en el momento de generar la imagen de mfsBSD, o en otras palabras, son usados por el archivo Makefile. También contiene algunos archivos que controlan el comportamiento de lo que se debe dejar para la imagen, como prunelist, o incluso otros que nos dejan incluir paquetes pre-compilados. Una lista más detallada de lo que nos concierne como usuarios sería la siguiente:

* *destroygeom*: un script que nos ahorra tiempo a la hora de eliminar las particiones en los proveedores GEOM que especifiquemos;
* *kern_exclude*: archivos que serán excluidos de /boot/kernel en la imagen final de mfsBSD;
* *motd, motd.se*: el archivo /etc/motd para imprimir el Mensaje del día. En su edición normal será usado el primero, mientras que en la edición especial será usado el segundo;
* *packages, packages-mini*: paquetes que serán incluidos en la imagen de mfsBSD. En su edición normal, y su edición -mini;
* *prunelist*: lista de archivos que serán excluidos de la imagen final de mfsBSD;
* *roothack/*: el encargado de descomprimir el root.txz en memoria;
* *zfsinstall*: un script que nos ahorra tiempo a la hora de particionar e instalar un entorno con ZFS.

Algunos archivos controlan el comportamiento de la imagen final, mientras que otros solo los usaremos cuando el sistema esté en ejecución, tales como zfsinstall y destroygeom, que protragonizarán la instalación de FreeBSD más adelante.

Aclarado el significado con la mayor sencillez posible, es hora de empezar a modificar los archivos. Cabe aclarar que si realizamos un listado de los directorios antes mencionados, como se verá más abajo, tienen una extensión peculiar.


```bash
$ ls tools/
destroygeom             do_gpt.sh               motd                    packages-mini.sample    prunelist               roothack
doFS.sh                 kern_exclude            motd.se                 packages.sample         prunelist.9             zfsinstall
$ ls conf/
authorized_keys.sample  hosts.sample            loader.conf.sample      rc.local.sample         ttys.sample
boot.config.sample      interfaces.conf.sample  rc.conf.sample          resolv.conf.sample
```

Los archivos .sample que comunmente vemos en algunos paquetes cuando los instalamos, son archivos de ejemplos que nos indican cómo se debería configurar ese pieza de software, o nos ayudan a orientarnos. En mfsBSD no solo son archivos de ejemplo, sino que son usados cuando su contraparte, los archivos sin esa extensión, no existe. Por ejemplo, si el archivo *packages* existe y existe también *packages.sample*, el primero se usará, pero si no fuera así, el segundo es el que se usará, pero si no existe ninguno de los dos, no se instalarían paquetes.

**loader.conf.sample**:

```bash
# $Id$
#
# This is the /boot/loader.conf of your image
#
# Custom mfsbsd variables
#
# Set all auto-detected interfaces to DHCP
#mfsbsd.autodhcp="YES"
#
# Define a new root password
#mfsbsd.rootpw="foobar"
#
# Alternatively define a root password hash like in master.passwd
# NOTICE: replace '$' characters with '%'
#mfsbsd.rootpwhash=""
#
# Add additional nameservers here
#mfsbsd.nameservers="192.168.1.1 192.168.1.2"
#
# Change system hostname
#mfsbsd.hostname="mfsbsd"
#
# List of interfaces to be set
#mfsbsd.interfaces="em0 em1"
#
# Individual configuration of each intac_interfaces="eth0"
#mfsbsd.ifconfig_eth0_mac="xx:xx:xx:xx:xx:xx"
#mfsbsd.ifconfig_eth0="inet 192.168.1.10/24"
#
# Default router
#mfsbsd.defaultrouter="192.168.1.1"
#
# List of static routes and their definitions
#mfsbsd.static_routes="r1 r2"
#mfsbsd.route_r1="-net 192.168.2 192.168.1.1"
#mfsbsd.route_r2="-net 192.168.3 192.168.1.1"

#
# Do not change anything here until you know what you are doing
#
mfs_load="YES"
mfs_type="mfs_root"
mfs_name="/mfsroot"
ahci_load="YES"
vfs.root.mountfrom="ufs:/dev/md0"
mfsbsd.autodhcp="YES"
```

Como se aclaró anteriormente, loader.conf es el archivo más importante porque nos permite ajustar casi todo sin recurrir a los otros archivos. El archivo de arriba, loader.conf.sample, es el archivo de ejemplo, y ya se mencionó que si no existe su contraparte, el archivo que no contiene extensión, se usará este en su lugar. Procedemos a copiar conf/loader.conf.sample a conf/loader.conf y modificaremos a partir de allí.


```bash
cp conf/loader.conf.sample conf/loader.conf
```

El archivo se explica a través de comentarios, pero explicaré lo que usaremos en este artículo en brevedad. Al menos que estemos conectados punto a punto o en una red segura, es mejor modificar la contraseña pre-determinada.


```bash
mfsbsd.rootpw="this is a very secure password.12345"
```

No obstante, si el sistema necesita ejecutarse para una tarea pre-definida y no una instalación de FreeBSD, es mejor usar una forma más apropiada por si acceden sin nuestro consentimiento a nuestro sistema.


```bash
mfsbsd.rootpwhash="%6%0DMGate/WoCrttXO%WQbsFPYr1LOjJlVzjt/PbP929J130YsAbaEfMm.FGwKsKQvC4xjJSrcM3YhHgoz/LOveMVToCZ3Jmghd/cJlw0"
```

Para poder generar una contraseña como esa, debemos usar *openssl passwd -6*, pero debemos cambiar los signos de dólar por signos de porcentaje, como se puede apreciar arriba.

Si deseamos configurar los *nameservers* en vez de usar los que el servidor DHCP nos mande, podemos hacerlo.


```bash
mfsbsd.nameservers="208.67.222.222 208.67.220.220"
```

Si queremos desactivar la autoconfiguración de todas las interfaces, podemos hacerlo.


```bash
mfsbsd.autodhcp="NO"
```

En el caso de desactivar la autoconfiguración, podemos especificar la interfaces que deseemos que se configuren usando DHCP.


```bash
mfsbsd.interfaces="em0 em1"
mfsbsd.ifconfig_em0="DHCP"
mfsbsd.ifconfig_em1="DHCP"
```

Si no sabemos cuál es o será nuestra interfaz de red, pero conocemos su dirección MAC, mfsBSD puede lidiar con esto.


```bash
mfsbsd.mac_interfaces="ext1"
mfsbsd.ifconfig_ext1_mac="64:74:78:64:66:2E"
mfsbsd.ifconfig_ext1="inet 192.168.1.10/24"
```

Cabe aclarar que así como podemos configurarlo desde conf/loader.conf, también podríamos hacer lo mismo en conf/interfaces.conf. (Ver el archivo para más destalles.)

No olvidemos configurar la ruta predeterminada cuando usemos direcciones IPs estáticas.


```bash
mfsbsd.defaultrouter="192.168.1.1"
```

Al menos que haya una buena razón para no usar DHCP, recomiendo usarlo.

Podemos usar el archivo *authorized_keys* para acceder al sistema de forma remota usando nuestra clave pública SSH.


```bash
cat ~/.ssh/id_ed25519.pub > conf/authorized_keys
```

Usa la clave pública correspondiente. En micaso estoy colocando la que se encuentra en mi directorio, siendo el tipo ED25519.

Por defecto, boot.config viene con el flag -D para bootear con la configuración dual de la consola (ver el manual de boot(8) para más detalles). En mi caso, para este artículo, prefiero dejarlo en -c, que no hace nada (no-op).


```bash
echo \-c > conf/boot.config
```

Hemos visto gran parte de la configuración que se usará en tiempo de ejecución, y como se aclaró, gran parte de esos archivos son triviales y no requieren explicación ya que son un estándar, tales como /etc/hosts, /etc/rc.conf, y demás, por lo que me limité a explicar lo que es específico de mfsBSD.

Ahora es tiempo de configurar la imagen en tiempo de compilación. Esta parte será más corta, ya que solo se explicará como agregar paquetes, siendo una cosa extremadamente sencilla. Excluir archivos, ya sea del world o del kernel, es algo no trivial y requiere un cuidadoso estudio para no dejar inservible las cosas. Eres libre de estudiar *prunelist* y *kern_exclude* para deshacer o rehacer lo que te plazca.

Resulta que es necesario multiplexar la pantalla para trabajar más eficiente, así que podemos elegir entre tmux o gnu-screen. Elegimos gnu-screen, por lo que se incluirá en conf/packages.


```bash
echo screen > tools/packages
```

Es requerido además fusefs-sshfs para montar un directorio a través de SSH usando FUSE. También es necesario smartmontools para hacer diagnósticos y pruebas al disco que estoy respaldando. Ya que estoy respaldando un sistema de archivos con UFS, necesito rsync para mandarlo a través de la red de forma eficiente. La lista sería la siguiente (sin excluir a screen, que lo colocamos anteriormente).


```bash
cat <<EOF >> tools/packages
fusefs-sshfs
smartmontools
rsync
EOF
```


### Últimos pasos

La configuración parece extremadamente larga y tediosa, pero la realidad es que no lo es, con el tiempo y si se está familiarizado con FreeBSD se podrá apreciar que es increiblemente trivial. Pero ya habiendo configurado a la medida nuestra imagen, ya es momento de generarla. Primero hay que decidir el formato, el cual pueden ser, actualmente, tres: ISO, IMG y .tar.gz. Cabe aclarar que, debido a que algunas operaciones requieren privilegios (como ejecutar el comando chown, cambiar las propiedad de algunos archivos, entre otros) después de que se descomprime base.txz. Si el pavor inunda sus pensamientos, es posible leer el Makefile o ejecutar *make -n* para ver qué comandos se ejecutarán.

#### IMG

```bash
# make BASE=freebsd-dist
```

#### ISO

```bash
# make iso BASE=freebsd-dist
```

#### .tar.gz

```bash
# make tar BASE=freebsd-dist
```

#### archivo .tar.gz GCE compatible

```bash
# make gce BASE=freebsd-dist
```

Aparte de los *targets* (ver make(1) para más detalles), el archivo Makefile interpreta algunas variables que también modificarán el comportamiento de la imagen. Uno que puede ser más importante de todos ellos, es SE, para generar la edición especial.

```bash
# make SE=1 BASE=freebsd-dist
```

Otras de las características de mfsBSD es que permite compilar el kernel y el world.

```bash
# make iso CUSTOM=1 BUILDWORLD=1 BUILDKERNEL=1
```

En el anterior comando, se compilará una imagen ISO, y también es posible compilar con los otros formatos vistos anteriormente.

Hay docenas de variables que podrían o no ser de utilidad. Abajo se pueden apreciar una lista de ellas, extraidas del Makefile (líneas de 6 a 193).

```bash
#
# User-defined variables
#
BASE?=			/cdrom/usr/freebsd-dist
KERNCONF?=		GENERIC
MFSROOT_FREE_INODES?=	10%
MFSROOT_FREE_BLOCKS?=	10%
MFSROOT_MAXSIZE?=	120m
ROOTPW_HASH?=		$$6$$051DfTvLymkY$$Z5f6snVFQJKugWmGi8y0motBNaKn9em0y2K0ZsJMku3v9gkiYh8M.OTIIie3RvHpzT6udumtZUtc0kXwJcCMR1

# If you want to build your own kernel and make you own world, you need to set
# -DCUSTOM or CUSTOM=1
#
# To make buildworld use 
# -DCUSTOM -DBUILDWORLD or CUSTOM=1 BUILDWORLD=1
#
# To make buildkernel use
# -DCUSTOM -DBUILDKERNEL or CUSTOM=1 BUILDKERNEL=1
#
# For all of this use
# -DCUSTOM -DBUILDWORLD -DBUILDKERNEL or CUSTOM=1 BUILDKERNEL=1 BUILDWORLD=1
#

#
# Paths
#
SRC_DIR?=		/usr/src
CFGDIR?=		conf
SCRIPTSDIR?=		scripts
PACKAGESDIR?=		packages
CUSTOMFILESDIR?=	customfiles
CUSTOMSCRIPTSDIR?=	customscripts
TOOLSDIR?=		tools
PRUNELIST?=		${TOOLSDIR}/prunelist
KERN_EXCLUDE?=		${TOOLSDIR}/kern_exclude
PKG_STATIC?=		/usr/local/sbin/pkg-static
#
# Program defaults
#
MKDIR?=		/bin/mkdir -p
CHOWN?=		/usr/sbin/chown
CAT?=		/bin/cat
PWD?=		/bin/pwd
TAR?=		/usr/bin/tar
GTAR?=		/usr/local/bin/gtar
CP?=		/bin/cp
MV?=		/bin/mv
RM?=		/bin/rm
RMDIR?=		/bin/rmdir
CHFLAGS?=	/bin/chflags
GZIP?=		/usr/bin/gzip
TOUCH?=		/usr/bin/touch
INSTALL?=	/usr/bin/install
LS?=		/bin/ls
LN?=		/bin/ln
FIND?=		/usr/bin/find
PW?=		/usr/sbin/pw
SED?=		/usr/bin/sed
UNAME?=		/usr/bin/uname
BZIP2?=		/usr/bin/bzip2
XZ?=		/usr/bin/xz
MAKEFS?=	/usr/sbin/makefs
SSHKEYGEN?=	/usr/bin/ssh-keygen
SYSCTL?=	/sbin/sysctl
PKG?=		/usr/local/sbin/pkg
OPENSSL?=	/usr/bin/openssl
CUT?=		/usr/bin/cut
#
WRKDIR?=	${.CURDIR}/work
#
BSDLABEL?=	bsdlabel
#
DOFS?=		${TOOLSDIR}/doFS.sh
SCRIPTS?=	mdinit mfsbsd interfaces packages
BOOTMODULES?=	acpi ahci
.if defined(LOADER_4TH)
BOOTFILES?=	defaults device.hints loader_4th *.rc *.4th
EFILOADER?=	loader_4th.efi
.else
BOOTFILES?=	defaults device.hints loader_lua lua
EFILOADER?=	loader_lua.efi
.endif
MFSMODULES?=	aesni crypto cryptodev ext2fs geom_eli geom_mirror geom_nop \
		ipmi ntfs nullfs opensolaris smbus snp tmpfs zfs
# Sometimes the kernel is compiled with a different destination.
KERNDIR?=	kernel
#
XZ_FLAGS?=
#

.if defined(V)
_v=
VERB=1
.else
_v=@
VERB=
.endif

.if !defined(ARCH)
TARGET!=	${SYSCTL} -n hw.machine_arch
.else
TARGET=		${ARCH}
.endif

.if !defined(RELEASE)
RELEASE!=	${UNAME} -r
.endif

.if !defined(PKG_ABI)
PKG_ABI!=	echo "FreeBSD:`${UNAME} -U | ${CUT} -c 1-2`:`${UNAME} -m`"
.endif

.if !defined(SE)
IMAGE_PREFIX?=	mfsbsd
.else
IMAGE_PREFIX?=	mfsbsd-se
.endif

IMAGE?=		${IMAGE_PREFIX}-${RELEASE}-${TARGET}.img
ISOIMAGE?=	${IMAGE_PREFIX}-${RELEASE}-${TARGET}.iso
TARFILE?=	${IMAGE_PREFIX}-${RELEASE}-${TARGET}.tar
GCEFILE?=	${IMAGE_PREFIX}-${RELEASE}-${TARGET}.tar.gz
_DISTDIR=	${WRKDIR}/dist/${RELEASE}-${TARGET}

.if !defined(DEBUG)
EXCLUDE=	--exclude *.symbols
.else
EXCLUDE=
.endif

# Roothack stuff
.if !defined(NO_ROOTHACK)
. if defined(ROOTHACK_FILE) && exists(${ROOTHACK_FILE})
ROOTHACK_PREBUILT=1
. else
ROOTHACK_FILE=	${WRKDIR}/roothack/roothack
. endif
.endif

# Check if we are installing FreeBSD 9 or higher
.if exists(${BASE}/base.txz) && exists(${BASE}/kernel.txz)
FREEBSD9?=	yes
BASEFILE?=	${BASE}/base.txz
KERNELFILE?=	${BASE}/kernel.txz
.else
BASEFILE?=	${BASE}/base/base.??
KERNELFILE?=	${BASE}/kernels/generic.??
.endif

.if defined(MAKEJOBS)
_MAKEJOBS=	-j${MAKEJOBS}
.endif

_ROOTDIR=	${WRKDIR}/mfs
_BOOTDIR=	${_ROOTDIR}/boot
.if !defined(NO_ROOTHACK)
_DESTDIR=	${_ROOTDIR}/rw
MFSROOT_FREE_INODES?=1%
MFSROOT_FREE_BLOCKS?=1%
.else
_DESTDIR=	${_ROOTDIR}
.endif

.if !defined(SE)
# Environment for custom build
BUILDENV?= env \
	NO_FSCHG=1 \
	WITHOUT_CLANG=1 \
	WITHOUT_DICT=1 \
	WITHOUT_GAMES=1 \
	WITHOUT_LIB32=1

# Environment for custom install
INSTALLENV?= ${BUILDENV} \
	WITHOUT_TOOLCHAIN=1
.endif

# Environment for ustom scripts
CUSTOMSCRIPTENV?= env \
	WRKDIR=${WRKDIR} \
	DESTDIR=${_DESTDIR} \
	DISTDIR=${_DISTDIR} \
	BASE=${BASE}

.if defined(FULLDIST)
NO_PRUNE=1
WITH_RESCUE=1
.endif
```

La salida del comando *make* construyendo una imagen, edición especial, en el formato .IMG, y los paquetes que incluimos anteriormente, sería la siguiente:

```text
Extractingase and kernel ... done
Copying base.txz and kernel.txz ... done
Removing selected files from distribution ... done
Copying out cdboot and EFI loader ... done
Installing configuration scripts and files ... done
Generating SSH host keys ... done
Copying use files ...
customfiles/ -> /usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/mfs/rw
customfiles/etc -> /usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/mfs/rw/etc
customfiles/etc/profile -> /usr/home/dtxdf-fbsd/Downloads/Clones/Gitub/mfsbsd-work/work/mfs/rw/etc/profile
 done
Configuring boot environment ...x ./
x ./linker.hints
x ./kernel
 done
Creating EFIboot image ...Creating `/usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/cdboot/efiboot.img'
/usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/cdboot/efiboot.img: 4039 sectors in 4039 FAT12 clusters (512 bytes/cluster)
BytesPerSec=512 SecPerClust=1 ResSectors=1 FATs=2 RootDirEnts=512 Sectors=4096 Media=0xf0 FATsecs=12 SecPerTrack=63 Heads=255 HiddenSecs=0
Populating `/usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/cdboot/efiboot.img'
Image `/usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/work/cdboot/efiboot.img' complete
 done
Installing pkgng ... done
Installing user packages ...
Updaing FreeBSD repository catalogue...
Fetching meta.conf: 100%    163 B   0.2kB/s    00:01
Fetching packagesite.pkg: 100%    6 MiB 146.6kB/s    00:46
Processing entries: 100%
FreeBSD repository update completed. 31196 packages processed.
All repositories are up to date.
The following 18 package(s) will be affected (of 0 checked):

New packages to be INSTALLED:
        fusefs-libs3: 3.10.5
        fusefs-sshfs: 3.7.2
        gettext-runtime: 0.21
        glib: 2.70.4_3,2
        indexinfo: 0.3.1
        libffi: 3.3_1
        libiconv: 1.16
        liblz4: 1.9.3,1
        libxml2: 2.9.13_1
        mpdecimal: 2.5.1
        pcre: 8.45_1
        python38: 3.8.13
        readline: 8.1.2
        rsync: 3.2.3_1
        screen: 4.9.0_4
        smartmontools: 7.3
        xxhash: 0.8.1
        zstd: 1.5.2

Number of packages to be installed: 18

The process will require 185 MiB more space.
33 MiB to be downloaded.
[1/18] Fetching screen-4.9.0_4.pkg: 100%  496 KiB 169.4kB/s    00:03
[2/18] Fetching fusefs-sshfs-3.7.2.pkg: 100%   40 KiB  40.9kB/s    00:01
[3/18] Fetching smartmontools-7.3.pkg: 100%  505 KiB 258.6kB/s    00:02
[4/18] Fetching rsync-3.2.3_1.pkg: 100%  355 KiB 181.6kB/s    00:02
[5/18] Fetching indexinfo-0.3.1.pkg: 100%    6 KiB   5.7kB/s    00:01
[6/18] Fetchig fusefs-libs3-3.10.5.pkg: 100%   95 KiB  97.3kB/s    00:01
[7/18] Fetching glib-2.70.4_3,2.pkg: 100%    3 MiB 261.7kB/s    00:13
[8/18] Fetching libxml2-2.9.13_1.pkg: 100%    1 MiB 237.0kB/s    00:06
[9/18] Fetching readline-8.1.2.pkg: 100%  361 KiB 184.6B/s    00:02
[10/18] Fetching python38-3.8.13.pkg: 100%   23 MiB 187.7kB/s    02:11
[11/18] Fetching mpdecimal-2.5.1.pkg: 100%  322 KiB 164.7kB/s    00:02
[12/18] Fetching libffi-3.3_1.pkg: 100%   39 KiB  40.1kB/s    00:01
[13/18] Fetching gettext-runtime-.21.pkg: 100%  166 KiB 169.8kB/s    00:01
[14/18] Fetching pcre-8.45_1.pkg: 100%    1 MiB 313.1kB/s    00:04
[15/18] Fetching libiconv-1.16.pkg: 100%  606 KiB 310.5kB/s    00:02
[16/18] Fetching xxhash-0.8.1.pkg: 100%   80 KiB  82.3kB/s    00:01
[17/18] Fetching zstd-1.5.2.pkg: 100%  471 KiB 241.0kB/s    00:02
[18/18] Fetching liblz4-1.9.3,1.pkg: 100%  131 KiB 134.5kB/s    00:01
Checking integrity... done (0 conflicting)
[1/18] Installing indexinfo-0.3.1...
[1/18] Extracting indexinfo-0.3.1: 100%
[2/18] Installing readline-8.1.2...
[2/18] Extracting readline-8.1.2: 100%
[3/18] Installing mpdecimal-2.5.1...
[3/18] Extracting mpdecimal-2.5.1: 100%
[4/18] Installing libffi-3.3_1...
[4/18] Extracting libffi-3.3_1: 100%
[5/18] Installing gettext-runtime-0.21...
[5/18] Extracting gettext-runtime-0.21: 100%
[6/18] Installing libxml2-2.9.13_1...
[6/18] Extracting libxml2-2.9.13_1: 100%
[7/18] Installing python38-3.8.13...
[7/18] Extracting python38-3.8.13: 100%
[8/18] Installing pcre-8.45_1...
[8/18] Extracting pcre-.45_1: 100%
[9/18] Installing libiconv-1.16...
[9/18] Extracting libiconv-1.16: 100%
[10/18] Installing liblz4-1.9.3,1...
[10/18] Extracting liblz4-1.9.3,1: 100%
[11/18] Installing fusefs-libs3-3.10.5...
[11/18] Extracting fusefs-libs3-3.10.5: 100%
[12/18] Installing glib-2.70.4_3,2...
[12/18] Extracting glib-2.70.4_3,2: 100%
[13/18] Installing xxhash-0.8.1...
[13/18] Extracting xxhash-0.8.1: 100%
[14/18] Installing zstd-1.5.2...
[14/18] Extracting zstd-1.5.2: 100%
[15/18] Installing screen-4.9.0_4...
[15/18] Extracting screen-4.9.0_4: 100%
[16/18] Installing fusefs-sshfs-3.7.2...
[16/18] Extracting fusefs-sshfs-3.7.2: 100%
[17/18] Installing smartmontools-7.3...
[17/18] Extracting smartmontools-7.3: 100%
[18/18] Installing rsync-3.2.3_1...
[18/18] Extracting rsync-3.2.3_1: 100%
=====
Message from python38-3.8.13:

--
Note that some standard Python modules are provided as separate ports
as they require additional dependencies. They are available as:

py38-gdbm       databases/py-gdbm@py38
py38-sqlite3    databases/py-sqlite3@py38
py38-tkinter    x11-toolkits/py-tkinter@py38
=====
Message from fusefs-libs3-3.10.5:

--
Install the FUSE kernel module (kldload fusefs) to use this port.
=====
Message from screen-4.9.0_4:

--
As of GNU Screen 4.4.0:

Note that there was fix to screen message structure field
responsible for $TERM handling, making it impossible
to attach to older versions.
=====
Message from fusefs-sshfs-3.7.2:

--
Basic Instructions:
There are three ways to do this:

1)
% sshfs -o idmap=user username@example.org: /path/to/mount/point

or

2)
% mount_fusefs auto /path/to/mount/point sshfs -o idmap=user \
       username@example.org:

or

3)
% env FUSE_DEV_NAME=/dev/fuse0 sshfs -o idmap=user \
       username@example.org:
% mount_fusefs /dev/fuse0 /path/to/mount/point

For further options see ``sshfs -h''.
=====
Message from smartmontools-7.3:

--
smartmontools has been installed

To check the status of drives, use the following:

        /usr/local/sbin/smartctl -a /dev/ad0    for first ATA/SATA drive
        /usr/local/sbin/smartctl -a /dev/da0    for first SCSI drive
        /usr/local/sbin/smartctl -a /dev/ada0   for first SATA drive

To include drive health information in your daily status reports,
add a line like the following to /etc/periodic.conf:
        daily_status_smart_devices="/dev/ad0 /dev/da0"
substituting the appropriate device names for your SMART-capable disks.

To enable drive monitoring, you can use /usr/local/sbin/smartd.
A sample configuration file has been installed as
/usr/local/etc/smartd.conf.sample
Copy this file to /usr/local/etc/smartd.conf and edit appropriately

To have smartd start at boot
        echo 'smartd_enable="YES"' >> /etc/rc.conf
  --- %           0.0 MiB / 0.6 MiB = 0.000                   0:02
```


La salida final sería la siguiente -excluyendo lo anteriormente visto:


```text
100 %        93.6 MiB / 485.9 MiB = 0.193   477 KiB/s      17:24
 done
echo roothack.full: /usr/lib/libc.a  >> .depend
cc  -O2 -pipe   -g -MD  -MF.depend.roothack.o -MTroothack.o -std=gnu99 -Wno-format-zero-length -nobuiltininc -idirafter /usr/lib/clang/11.0.1/include -fstack-protector-strong -Wsystem-headers -Werror -Wall -Wno-format-y2k -W -Wno-unused-parameter -Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith -Wreturn-type -Wcast-qual -Wwrite-strings -Wswitch -Wshadow -Wunused-parameter -Wcast-align -Wchar-subscripts -Winline -Wnested-externs -Wredundant-decls -Wold-style-definition -Wno-pointer-sign -Wmissing-variable-declarations -Wthread-safety -Wno-empty-body -Wno-string-plus-int -Wno-unused-const-variable  -Qunused-arguments    -c /usr/home/dtxdf-fbsd/Downloads/Clones/Github/mfsbsd-work/tools/roothack/roothack.c -o roothack.o
cc -O2 -pipe -g -std=gnu99 -Wno-format-zero-length -nobuiltininc -idirafter /usr/lib/clang/11.0.1/include -fstack-protector-strong -Wsystem-headers -Werror -Wall -Wno-format-y2k -W -Wno-unused-parameter -Wstrict-prototypes -Wmissing-prototypes -Wpointer-arith -Wreturn-type -Wcast-qual -Wwrite-strings -Wswitch -Wshadow -Wunused-parametr -Wcast-align -Wchar-subscripts -Winline -Wnested-externs -Wredundant-decls -Wold-style-definition -Wno-pointer-sign -Wmissing-variable-declarations -Wthread-safety -Wno-empty-body -Wno-string-plus-int -Wno-unused-const-variable -Qunused-arguments  -static   -o roothack.full roothack.o  -larchive -lbz2 -lz -llzma -lcrypto -lbsdxml -lmd -lprivatezstd
objcopy --only-keep-debug roothack.full roothack.debug
objcopy --strip-debug --add-gnu-debuglink=roothack.debug  roothack.full roothack
Installing roothack ... done
Creating and compressing mfsroot ... done
Copying FreeBSD installation image ... done
Creating image file ...423968+0 records in
423968+0 records out
434143232 bytes transferred in 8.400635 secs (51679810 bytes/sec)
423936+0 records in
423936+0 records out
434110464 bytes transferred in 8.056553 secs (53882906 bytes/sec)
md0 created
md0p1 added
partcode written to md0p1
bootcode written to md0
md0p2 added
16+0 records in
16+0 records out
2097152 bytes transferred in 0.135044 secs (15529442 bytes/sec)
md0p3 added
Calculated size of `./mfsbsd-se-13.0-RELEASE-p11-amd64.img.jkCqJ9Vx': 390168576 bytes, 44 inodes
Extent size set to 32768
./mfsbsd-se-13.0-RELEASE-p11-amd64.img.jkCqJ9Vx: 372.1MB (762048 sectors) block size 32768, fragment size 4096
        using 1 cylinder groups of 372.09MB, 11907 blks, 256 inodes.
super-block backups (for fsck -b #) at:
 64,
Populating `./mfsbsd-se-13.0-RELEASE-p11-amd64.img.jkCqJ9Vx'
Image `./mfsbsd-se-13.0-RELEASE-p11-amd64.img.jkCqJ9Vx' complete
2976+1 records in
2976+1 reords out
390168576 bytes transferred in 23.367620 secs (16696975 bytes/sec)


 done
-rw-r--r--  1 root  dtxdf-fbsd  434143232 Ju 29 15:15 mfsbsd-se-13.0-RELEASE-p11-amd64.img
```

Ahora solo se necesita grabar esa imagen a nuestra llave USB co se especificó en anteriores secciones.

# Instalación de FreeBSD usando mfsBSD

Hay tres o cuatro formas en los que podríamos instalar FreeBSD usando mfsBSD. La primera es ejecutando bsdinstall(8) dentro de nuestro host con mfsBSD, que esa sería la forma más sencilla y la más conocida. La segunda es usar destroygeom y zfsinstal para instalar de forma automatizada un entorno con ZFS. La tercera es cuando queremos usar UFS o ZFS pero necesitamos personalar nuestra instalación al máximo, pero esta es la más complicada si se desconoce el proceso de instalación de FreeBSD. La cuarta, es simplemente cualquier cosa que nuestra imaginación nos limite.

## Preparación

Dependiendo dela opción que hayamos elegidos, ya sea la edición normal o la edición especial, el procedimiento de preparación será sutilmte diferente.

### Sin archivos de distribución

En anteriores secciones mostramos cómdescargar los archivos de distribución, pero eso es útil solo si se descargarán a través del directorio web o FTP público. osotros también podemos montar nuestro propio servidor web o FTP. Para el caso de seleccionar la primera opción, se tiene una ariedad de opciones, desde Apache, hasta NGINX, desde lighttpd hasta obhttpd (el servidor web de OpenBSD), o puedes decantarte, or el momento, con el servidor web integrado de Python mientras reflexionas mejor tus elecciones. Si se elegirá un servidor FTP, FreeBSD ya incluye uno, incluso un servidor TFTP. No obstante, las elecciones son parte de la libertad, por lo que hasta es posible descargar los archivos a través de SSH (usando SCP o SFTP).

### Con archivos de distribución

Si se eligió la edición especial, no es necesario hacer más nada, ya que los archivos vienen incluidos en la misma imagen. El único paso adicional que haría falta sería montar la partición de datos de mfsBSD, que se puede realizar de la siguiente manera:


```bash
mkdir /media/wrk
mkdir /media/tmp
mount /dev/da4p3 /media/wrk
```

Se debe elegir el dispositivo correcto, en mi caso es el dispositivo da4 con su partición número 3. La razón por la que creo dos directorios se entienden mejor más adalente, pero en suma, simplemente es el directorio de trabajo, que es donde tenemos los datos, y el otro es el directorio que usará zfsinstall como veremos más adelante.

## bsdinstall: haciendo las cosas al modo tradicional

La principal razón de utilizar mfsBSD es la personalización y las ventajas que nos coloca encima de la mesa para la automatización, pero resulta que solo queremos sacar ventajas es de su imagen reducida en comparación con la imagen oficial de FreeBSD, por lo que no es necesario que nos compliquemos más de lo necesario.

```bash
env BSDINSTALL_DISTDIR=/media/wrk/13.0-RELEASE-p11-amd64 bsdinstall auto
```

Un solo comando es lo único que se necesita para configurar e instalar FreeBSD, ya que bsdinstall(8) hace esto por nosotros. La variable BSDINSTALL_DISTDIR que está ajustada en /usr/freebsd-dist por defecto, es necesario cambiarla para indicar la nueva ruta donde están nuestros archivos de distribución.

Ahora solo sigue la instalación como lo harías normalmente.

## zfsinstall: automatizando el progreso

zfsinstall es un script que viene incluido en el conjunto de scripts de mfsBSD. Este se puede leer desde la ruta /root/bin, cuando la imagen está corriendo, aunque es posible ejecutarlo sin necesidad de especificar su ruta absoluta.

```bash
# zfsinstall -h

Install FreeBSD using ZFS from a compressed archive

Required flags:
-d geom_provider  : geom provider(s) to install to (e.g. da0)

Optional flags:
-u dist_url       : URL or directory with base.txz and kernel.txz
                    (defaults to FreeBSD FTP mirror)
-r raidz|mirror   : select raid mode if more than one -d provider given
-s swap_part_size : create a swap partition with given size (default: no swap)
-z zfs_part_size  : create zfs parition of this size (default: all space left)
-p pool_name      : specify a name for the ZFS pool (default: tank)
-C                : compatibility mode with limited feature flags
                    (enable only async_destroy, empty_bpobj and lz4_compress)
-m mount_point    : use this mount point for operations (default: /mnt)
-c                : enable compression for all datasets
-l                : use legacy mounts (via fstab) instead of ZFS mounts
-4                : use fletcher4 as default checksum algorithm
-A                : align partitions to 4K blocks

Examples:
Install on a single drive with 2GB swap:
tools/zfsinstall -u /path/to/release -d da0 -s 2G
Install on a mirror without swap, pool name rpool:
tools/zfsinstall -u /path/to/release -d da0 -d da1 -r mirror -p rpool

Notes:
When using swap and raidz/mirror, the swap partition is created on all drives.
The /etc/fstab entry will contatin only the first drive's swap partition.
You can enable all swap partitions and/or make a gmirror-ed swap later.
```

Su uso es extremadamente sencillo, y se puede apreciar arriba su ayuda y su sintaxis, pero más abajo se ven algunos ejemplos usando dispositivos reales y rutas reales desde mi sistema, aunque antes de proseguir, es necesario saber que existe destroygeom, la contrparte de zfsinstall, ya que este borra todas las particiones y el zpool (si se lo indicamos).

### destroygeom: borrando todas las particiones de todos nuestros proveedores GEOM

```bash
# destroygeom -d da0 -d da1 -d da2 -d da3
Destroying geom da0:
    Deleting partition 1 ... done
    Deleting partition 2 ... done
    Deleting partition 3 ... done
Destroying geom da1:
    Deleting partition 1 ... done
    Deleting partition 2 ... done
    Deleting partition 3 ... done
Destroying geom da2:
    Deleting partition 1 ... done
    Deleting partition 2 ... done
    Deleting partition 3 ... done
Destroying geom da3:
    Deleting partition 1 ... done
    Deleting partition 2 ... done
    Deleting partition 3 ... done
```

### zfsinstall: el modo más básico

```bash
zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -s 2G -m /media/tmp -cA
```

**salida**:

```text
# zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -s 2G -m /media/tmp -cA

Creating GUID partitions on da0 ... done
Configuring ZFS bootcode on da0 ... done
=>        40  1172123488  da0  GPT  (559G)
          40         472    1  freebsd-boot  (236K)
         512        3584       - free -  (1.8M)
        4096     4194304    2  freebsd-swap  (2.0G)
     4198400  1167921152    3  freebsd-zfs  (557G)
  1172119552        3976       - free -  (1.9M)

Creating ZFS pool tank on  da0p3 ... done
Enabling default compression on tank ... done
Creating tank root partition: ... done
Creating tank partitions: var tmp ... done
Setting bootfs for tank to tank/root ... done
NAME            USED  AVAIL     REFER  MOUNTPOINT
tank            248K   539G       24K  none
tank/root        73K   539G       25K  /media/tmp
tank/root/tmp    24K   539G       24K  /media/tmp/tmp
tank/root/var    24K   539G       24K  /media/tmp/var
Extracting FreeBSD distribution ...done
Writing /boot/loader.conf... done
Writing /etc/fstab...Writing /etc/rc.conf... done
Copying /boot/zfs/zpool.cache ... done
Installation complete.
The system will boot from ZFS with clean install on next reboot

You may make adjustments to the installe system using chroot:
chroot /media/tmp

Some adjustments may require a mounted devfs:
mount -t devfs devfs /media/tmp/dev

WARNNG - Don't export ZFS pool "tank"!
```

El comando anterior usa (con -u) /media/wrk/13.0-RELEASE-p11-amd64 como el directorio donde se encuentran los archivos de distribución. Usa el proveedor GEOM (con -d) da0. Crea una partición (con -s) de 2G para la memoria de intercambio. Usa (con -m) /media/tmp como punto de montaje temporal donde se encuentran extraidos los archivos kernel.txz y base.txz. Y por último, pero no menos importante, -c habilita la compresión para todos los datasets, y -A alinea las particiones en bloques de 4 KiB.

### zfsinstall: mirror entre dos proveedores

```bash
zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -d da1 -r mirror -s 2G -m /media/tmp -cA
```

**salida**:

```text
# zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -d da1 -r mirror -s 2G -m /media/tmp -cA
Creating GUID partitions on da0 ... done
Configuring ZFS bootcode on da0 ... done
=>        40  1172123488  da0  GPT  (559G)
          40         472    1  freebsd-boot  (236K)
         512        3584       - free -  (1.8M)
        4096     4194304    2  freebsd-swap  (2.0G)
     4198400  1167921152    3  freebsd-zfs  (557G)
  1172119552        3976       - free -  (1.9M)

Creating GUID partitions on da1 ... done
Configuring ZFS bootcode on da1 ... done
=>        40  1172123488  da1  GPT  (559G)
          40         472    1  freebsd-boot  (236K)
         512        3584       - free -  (1.8M)
        4096     4194304    2  freebsd-swap  (2.0G)
     4198400  1167921152    3  freebsd-zfs  (557G)
  1172119552        3976       - free -  (1.9M)

Creating ZFS pool tank on  da0p3 da1p3 ... done
Enabling default compression on tank ... done
Creating tank root partition: ... done
Creating tank partitions: var tmp ... done
Setting bootfs for tank to tank/root ... done
NAME            USED  AVAIL     REFER  MOUNTPOINT
tank            252K   539G       24K  none
tank/root        73K   539G       25K  /media/tmp
tank/root/tmp    24K   539G       24K  /media/tmp/tmp
tank/root/var    24K   539G       24K  /media/tmp/var
Extracting FreeBSD distribution ... done
Writing /boot/loader.conf... done
Writing /etc/fstab...Writing /etc/rc.conf... done
Copying /boot/zfs/zpool.cache ... done

Installation complete.
The system will boot from ZFS with clean install on next reboot

You may make adjustments to the installed system using chroot:
chroot /media/tmp

Some adjustments may require a mounted devfs:
mount -t devfs devfs /media/tmp/dev

WARNING - Don't export ZFS pool "tank"!
```

Este modo es "más complicado" que el anterior. No explicaré las opciones que ya expliqué anteriormente, por lo que me limitaré a decir que hemos agregado un nuevo proveedor (-d da1) y hemos ajustado el tipo del raid a *mirror*.

## zfsinstall 2.0: modernización, lo que necesitaba

**Jueves, 30 de Junio del 2022 - Nota**: ya no es necesario clonar la bifurcación del proyecto, los cambios han sido aplicados (ver pull request [#136](https://github.com/mmatuska/mfsbsd/pull/136)).

zfsinstall es genial, y se lo agradezco a Martin Matuska por su esfuerzo en hacer mfsBSD realidad, junto con todos sus colaboradores, pero si algo realmente necesitaba que zfsinstall tuviera es un poco más de flexibilidad. Si ya conoces un poco de RAID y de ZFS y sus RAIDZ, sabes que él te brinda la posibilidad de concatenar diferentes arreglos, o incluso puedes usar varios proveedores GEOM sin que te importe la redundacia, o en otras palabras, hacer stripping de varios proveedores. Esto es algo que zfsinstall no tiene incluido.

Aunque podría crear un script propio y privado que hiciera eso por mí, ¿por qué no compartirlo a las personas de Internet? Claro que puedo, pero estaría reinventando la rueda, zfsinstall está allí, y mfsBSD es un proyecto que tiene tiempo, así que preferí colaborar con un necesidad, que no solo es mía (ver issue [#133](https://github.com/mmatuska/mfsbsd/issues/133)). Para poder usarlo, o puedes dirigirte a la bifurcación que hice y que está a la par con mfsBSD (https://github.com/DtxdF/mfsbsd) y copiar tools/zfsinstall, o puedes simplemente clonarlo.

He aquí la salida de la ayuda de zfsinstall 2.0 (perdonen por el 2.0, en realidad no es 2.0, es simplemente zfsinstall, pero es para que sepan la diferencia):

```bash
# zfsinstall -h

Install FreeBSD using ZFS from a compressed archive

Required flags:
-d geom_provider  : geom provider(s) to install to (e.g. da0)

Optional flags:
-r raidz|mirror   : select raid mode if more than one -d provider given
                    (must begin after -d)
-u dist_url       : URL or directory with base.txz and kernel.txz
                    (defaults to FreeBSD FTP mirror)
-s swap_part_size : create a swap partition with given size (default: no swap)
-z zfs_part_size  : create zfs parition of this size (default: all space left)
-p pool_name      : specify a name for the ZFS pool (default: tank)
-C                : compatibility mode with limited feature flags
                    (enable only async_destroy, empty_bpobj and lz4_compress)
-m mount_point    : use this mount point for operations (default: /mnt)
-c                : enable compression for all datasets
-l                : use legacy mounts (via fstab) instead of ZFS mounts
-4                : use fletcher4 as default checksum algorithm
-A                : align partitions to 4K blocks

Examples:
Install on a single drive with 2GB swap:
/root/bin/zfsinstall -u /path/to/rlease -d da0 -s 2G
Install on four-disk stripe:
/root/bin/zfsinstall -u /path/to/release -d da0 -d da1 -d da2 -d da3
Install on an stripped mirror:
/root/bin/zfsinstall -u /path/to/release -d da0 -d da1 -r mirror -d da2 -d da3 -r mirror
Install on a mirror without swap, pool name rpool:
/root/bin/zfsinstall -u /path/to/release -d da0 -d da1 -r mirror -p rpool

Notes:
When using swap and raidz/mirror, the swap partition is created on all drives.
The /etc/fstab entry will contatin only the first drive's swap partition.
You can enable all swap partitions and/or make a gmirror-ed swap later.
```

Si somos observadores, notamos que hay cosas nuevas, como que ahora es posible hacer stripping entre varios dispositivos, realizar raidz1, raidz2 y raidz3, que es algo que no se podía anteriormente, y también es posible crear un stripped mirror, algo que constantemente tengo que usar y que probablemente a alguien de Internet le sea de utilidad. A continuación algunos ejemplos con las nuevas opciones.

### zfsinstall: stripped mirror entre cuatro proveedores

```bash
zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -d da1 -r mirror -d da2 -d da3 -r mirror -s 2G -m /media/tmp -cA
```

**salida**:

```text
# zfsinstall -u /media/wrk/13.0-RELEASE-p11-amd64 -d da0 -d da1 -r mirror -d da2 -d da3 -r mirror -s 2G -m /media/tmp -cA
Creating GUID partitions on da0 ... done
Configuring ZFS bootcode on da0 ... done
=>        40  1172123488  da0  GPT  (559G)
          40         472    1  freebsd-boot  (236K)
         512        3584       - free -  (1.8M)
        4096     4194304    2  freebsd-swap  (2.0G)
     4198400  1167921152    3  freebsd-zfs  (557G)
  1172119552        3976       - free -  (1.9M)

Creating GUID partitions on da1 ... done
Configuring ZFS bootcode on da1 ... done
=>        40  1172123488  da1  GPT  (559G)
          40         472    1  freebsd-boot  (236K)
         512        3584       - free -  (1.8M)
        4096     4194304    2  freebsd-swap  (2.0G)
     4198400  1167921152    3  freebsd-zfs  (557G)
  1172119552        3976       - free -  (1.9M)

Creating GUID partitions on da2 ... done
Configuring ZFS bootcode on da2 ... done
=>       40  143374576  da2  GPT  (68G)
         40        472    1  freebsd-boot  (236K)
        512       3584       - free -  (1.8M)
       4096    4194304    2  freebsd-swap  (2.0G)
    4198400  139173888    3  freebsd-zfs  (66G)
  143372288       2328       - free -  (1.1M)

Creating GUID partitions on da3 ... done
Configuring ZFS bootcode on da3 ... done
=>       40  143374576  da3  GPT  (68G)
         40        472    1  freebsd-boot  (236K)
        512       3584       - free -  (1.8M)
       4096    4194304    2  freebsd-swap  (2.0G)
    4198400  139173888    3  freebsd-zfs  (66G)
  143372288       2328       - free -  (1.1M)

Creating ZFS pool tank on  da0p3 da1p3 da2p3 da3p3 ... done
Enabling default compression on tank ... done
Creating tank root partition: ... done
Creating tank partitions: var tmp ... done
Setting bootfs for tank to tank/root ... done
NAME            USED  AVAIL     REFER  MOUNTPOINT
tank            253K   603G       24K  none
tank/root        73K   603G       25K  /media/tmp
tank/root/tmp    24K   603G       24K  /media/tmp/tmp
tank/root/var    24K   603G       24K  /media/tmp/var
Extracting FreeBSD distribution ... done
Writing /boot/loader.conf... done
Writing /etc/fstab...Writing /etc/rc.conf... done
Copying /boot/zfs/zpool.cache ... done

Installation complete.
The system will boot from ZFS with clean install on next reboot

You may make adjustments to the installed system using chroot:
chroot /media/tmp

Some adjustments may require a mounted devfs:
mount -t devfs devfs /media/tmp/dev

WARNING - Don't export ZFS pool "tank"!
```

Lo único requerido por zfsinstall 2.0 es que los proveedores (-d) deben definirse antes que el tipo de raid (-r), ya que el orden importan para getopts.

## instalación manual+scripting: porque te gustan las cosas a tu modo

La instalación manual es de la más rica en aprendizaje y las más complicadas, pero solo cuando somos primerizos y vemos toda la flexibilidad en nuestras manos. FreeBSD (y por lo tanto mfsBSD) nos pone las cosas muy fáciles y un modo de instalación muy sencillo de aprender.

No obstante, recomiendo al lector no limitarse solo a copiar los comandos que aquí presento y lo invito a leer las páginas del manual que muchos se esforzaron por crear.

### UFS

La instalación de UFS es sencilla solo cuando queremos mantenerla sencilla. Usar gmirror y gstripe en conjunto es algo que se escapa de este artículo dedicado a mfsBSD, pero que sin duda, con un poco de esfuerzo, es posible lograr.

Lo ideal no sería solo ejecutar comandos, para particionar, descomprimir los archivos de distribución, configurar los archivos de FreeBSD que se usará después de la instalación, etc., es sin duda mejor, o más tolerable, escribirlos en un script, que el sistema con mfsBSD lo descargue, ya sea manual, o en otras palabras, conectándonos a través de SSH como le venimos haciendo todo este tiempo, o podemos crear una imagen y le incluimos, ya sea a través de rc.local o un servicio en /etc/rc.d usando el directorio **customfiles/** que deberá estar al momento de crear nuestra imagen. (Ver la variable CUSTOMFILESDIR en Makefile.)

Este script o servicio deberá descargar de un servidor específico los archivos de distribución -al menos que hayamos usado la edición especial, y además, deberá descargar el script o al menos que ya venga incluido, para que particione y configure todo lo demás. Para esta sección nos limitaremos a usar la edición especial y descargaremos el script manualmente a través de SSH. El código fuente del script es el que se puede apreciar más abajo.

**install-ufs.sh**:

```bash
#!/bin/sh -x

# Destruimos el esquema de particionado actual y
# creamos uno nuevo, que en este caso es GPT.
gpart destroy -F da0
gpart create -s gpt da0

# Particionamos
gpart add -t freebsd-boot -a 1m -l boot0 -s 512k da0
gpart add -t freebsd-swap -a 1m -l swap0 -s 2g da0
gpart add -t freebsd-ufs -a 1m -l root0 da0

# Incrustamos el código de arranque dentro del esquema
# de los metadatos del esquema de particionado actual
# y escribimos escribimos el código de arranque en
# la partición de booteo.
gpart bootcode -b /boot/pmbr -p /boot/gptboot -i 1 da0

# Formateamos la partición UFS y activamos journaling
newfs -j /dev/gpt/root0

# Montamos la partición donde se encuentran los archivos
# de distribución y montamos la partición root del disco
# donde instalaremos FreeBSD.
mkdir /media/wrk
fsck -p /dev/gpt/rootfs
mount /dev/gpt/rootfs /media/wrk
mount /dev/gpt/root0 /mnt

# Descomprimimos base.txz y kernel.txz dentro de la
# partición del disco actual.
tar --unlink -xpf /media/wrk/13.0-RELEASE-p11-amd64/base.txz -C /mnt
tar --unlink -xpf /media/wrk/13.0-RELEASE-p11-amd64/kernel.txz -C /mnt

# Escribimos en fstab las particiones que deberán ser montadas
# al inicio de nuestro sistema.
echo /dev/gpt/root0		/	ufs	rw	0	1 > /mnt/etc/fstab
echo /dev/gpt/swap0		none	swap	sw	0	0 >> /mnt/etc/fstab

# Configuramos algunos parámetros e iniciamos algunos servicios.
echo clear_tmp_enable="YES" >> /mnt/etc/rc.conf
echo syslogd_flags="-ss" >> /mnt/etc/rc.conf
echo hostname="bob" >> /mnt/etc/rc.conf
echo keymap="es.acc.kbd" >> /mnt/etc/rc.conf
echo sshd_enable="YES" >> /mnt/etc/rc.onf
echo ifconfig_em1="DHCP" >> /mnt/etc/rc.conf

# Cambiamos la contraseña del superusuario.
chroot /mnt passwd

# Agregamos un nuevo usuario administrativo y
# le asignamos una contraseña.
mkdir -p /mnt/home/bob
pw useradd -R /mnt -n bob -c "Bob Stephan" -G wheel
chroot /mnt passwd bob

# Reiniciamos
reboot
```

Quiero dejar algunas notas. Primero, este script es demostrativo, no debería ser usado en un entorno real, lo digo porque muchas cosas, que no deberían, están hardcodeadas, además tener una gran partición no es buena idea, es mejor particionar según lo que vayamos a almacenar allí. Segundo, usen *diskinfo -s* para obtener el serial del disco con el fin de identificar cada partición de forma única. Identificar de esto mvita muchos problemas a la hora cambiar un disco o de identificar cuál es el que está en mal estado. En mi caso me gusta usar los últimos caracteres del serial del disco duro como sufijo, y como prefijo uso dos o tres caracteres máximo, estos son *rt/i], para la partición root, [i]sw*, para la memoria de intercambio, *tmp*, para la partición tmp, si es que se usará esta partición aparte; y la lista sigue y sigue, el punto es que se deben realizar esta clase de organización para evitar problemas innecesarios.

Una vez aclarado eso, simplemente copiemos el script usando, por ejemplo, scp para luego  ejecutarlo de foma remota.

```bash
$ scp install-ufs.sh root@10.0.0.16:/tmp
$ ssh root@10.0.0.16 sh /tmp/install-ufs.sh
```

El script es útil para el aprendizaje, en este caso, sobre cómo instalar FreeBSD de forma manual. Lo comenté, por lo que la explicación de lo que hace está en su mismo código fuente.

### ZFS

Uno pensaría, al escuchar sobre ZFS, que es más complejo de instalar que UFS, y la respuestas es, depende. La manera más fácil y rápida de instalar ZFS de forma manual es, primero, usar bsdinstall(8) para instalar un entorno con ZFS. Una vez en el entorno, ejecutar *zpool history* para obtener todos los comandos que ejecutó bsdinstall(8). FreeBSD configura de un modo específico los datasets para que puedan o no ser usados cuando creamos boot environments, por lo que es importante seguir esta regla, más que una convención. La salida de mi *zpool history* (casi dos meses que tengo este archivo) sería la que se muestra abajo.


```text
2022-05-11.00:58:03 zpool create -o altroot=/mnt -O compress=zstd -O atime=off -m none -f zroot mirror /dev/gpt/zfsXXXXX /dev/gpt/zfsYYYYY
2022-05-11.00:58:22 zfs create -o mountpoint=none zroot/ROOT
2022-05-11.00:58:58 zfs create -o mountpoint=/ zroot/ROOT/default
2022-05-11.00:59:23 zfs create -o mountpoint=/tmp -o exec=off -o setuid=off zroot/tmp
2022-05-11.00:59:43 zfs create -o mountpoint=/usr -o canmount=off zroot/usr
2022-05-11.00:59:56 zfs create zroot/usr/home
2022-5-11.01:00:22 zfs create -o setuid=off zroot/usr/ports
2022-05-11.01:00:38 zfs create zroot/usr/src
2022-05-11.01:01:04 zfs create -o mountpoint=/var -o canmount=off zroot/var
2022-05-11.01:01:24 zfs create -o exec=off -o setuid=off zroot/var/audit
2022-05-11.01:01:26 zfs create -o exec=off -o setuid=off zroot/var/crash
2022-05-11.01:01:32 zfs create -o exec=off -o setuid=off zroot/var/log
2022-05-11.01:01:42 zfs create -o atime=on zroot/var/mail
2022-05-11.01:01:54 zfs create -o setuid=off zroot/var/tmp
2022-05-11.01:02:21 zfs set mountpoint=/zroot zroot
2022-05-11.01:02:42 zpool set bootfs=zroot/ROOT/default zroot
2022-05-11.01:03:19 zpool set cachefile=/mnt/boot/zfs/zpool.cache zroot
2022-05-11.01:03:34 zfs set canmount=noauto zroot/ROOT/default
```

He sustituido los seriales con Xs y Ys de las particiones ZFS.

Ya hemos instalado UFS en la anterior sección, por lo que tendremos más experiencia a la hora de saber cómo proceder con la instalación de FreeBSD pero esta vez usando ZFS. Lo que comunmente se debe hacer para instalar ZFS es crear tres particiones, muy parecido a lo que vimos en UFS, solo que como ZFS us datasets, no tiene la desventajas de usar una gran partición.

**install-zfs.sh**:

```bash
#!/bin/sh -x

# Destruimos el esquema de particionado actual y
# creamos uno nuevo, que en este caso es GPT.
gpart destroy -F da0
gpart create -s gpt da0
# lo mismo pero con otro disco
gpart destroy -F da1
gpart create -s gpt da1

# Particionamos
gpart add -t freebsd-boot -a 1m -l boot0 -s 512k da0
gpart add -t freebsd-swap -a 1m -l swap0 -s 2g da0
gpart add -t freebsd-zfs -a 1m -l root0 da0
# Idem
gpart add -t freebsd-boot -a 1m -l boot1 -s 512k da1
gpart add -t freebsd-swap -a 1m -l swap1 -s 2g da1
gpart add -t freebsd-zfs -a 1m -l root1 da1

# Incrustamos el código de arranque dentro del esquema
# de los metadatos del esquema de particionado actual
# y escribimos escribimos el código de arranque en
# la partición de booteo.
gpart bootcode -b /boot/pmbr -p /boot/gpzfstboot -i 1 da0
# Idem
gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 da1

zpool create -o altroot=/mnt -O compress=zstd -O atime=off -m none -f zroot mirror /dev/gpt/root0 /dev/gpt/root1
zfs create -o mountpoint=none zroot/ROOT
zfs create -o mountpoint=/ zroot/ROOT/default
zfs create -o mountpoint=/tmp -o exec=off -o setuid=off zroot/tmp
zfs create -o mountpoint=/usr -o canmount=off zroot/usr
zfs create zroot/usr/home
zfs create -o setuid=off zroot/usr/ports
zfs create zroot/usr/src
zfs create -o mountpoint=/var -o canmount=off zroot/var
zfs create -o exec=off -o setuid=off zroot/var/audit
zfs create -o exec=off -o setuid=off zroot/var/crash
zfs create -o exec=off -o setuid=off zroot/var/log
zfs create -o atime=on zroot/var/mail
zfs create -o setuid=off zroot/var/tmp
zfs set mountpoint=/zroot zroot
zpool set bootfs=zroot/ROOT/default zroot
mkdir -p /mnt/boot/zfs
zpool set cachefile=/mnt/boot/zfs/zpool.cache zroot
zfs set canmount=noauto zroot/ROOT/default

# Montamos la partición donde se encuentran los archivos
# de distribución.
mkdir /media/wrk
fsck -p /dev/gpt/rootfs
mount /dev/gpt/rootfs /media/wrk

# Descomprimimos base.txz y kernel.txz dentro de la
# partición del disco actual.
tar --unlink -xpf /media/wrk/13.0-RELEASE-p11-amd64/base.txz -C /mnt
tar --unlink -xpf /media/wrk/13.0-RELEASE-p11-amd64/kernel.txz -C /mnt

# Escribimos en fstab las particiones que deberán ser montadas
# al inicio de nuestro sistema.
echo /dev/gpt/swap0		none	swap	sw	0	0 > /mnt/etc/fstab
echo /dev/gpt/swap1		none	swap	sw	0	0 >> /mnt/etc/fstab

# Configuramos algunos parámetros e iniciamos algunos servicios.
echo zfs_enable="YES" >> /mnt/etc/rc.conf
echo clear_tmp_enable="YES" >> /mnt/etc/rc.conf
echo syslogd_flags="-ss" >> /mnt/etc/rc.conf
echo hostname="bob" >> /mnt/etc/rc.conf
echo keymap="es.acc.kbd" >> /mnt/etc/rc.conf
echo sshd_enable="YES" >> /mnt/etc/rc.conf
echo ifconfig_em1="DHCP" >> /mnt/etc/rc.conf

# Cargamos el módulo ZFS desde el inicio
echo zfs_load="YES" >> /mnt/boot/loader.conf

# Cambiamos la contraseña del superusuario.
chroot /mnt passwd

# Agregamos un nuevo usuario administrativo y
# le asignamos una contraseña.
mkdir -p /mnt/home/bob
pw useradd -R /mnt -n bob -c "Bob Stephan" -G wheel
chroot /mnt passwd bob

# Reiniciamos
reboot
```

Se puede notar que este script está basado en el anterior de UFS, pero con una sútil pero efectiva modificación de la salida de *zpool history*. Además, esta vez hay que instalar el código de arranque por cada partición de cada disco duro, ya que si uno falla, será transparente para nosotros.

# Conclusión

Hemos apreciado el potencial de mfsBSD. Hemos visto cómo instalar FreeBSD usando ayudantes como bsdinstall(8), zfsinstall e incluso lo hemos instalado manualmente a través de un script. El potencial de esto solo se reduce a la imaginación y al problema que se esté deseando solucionar. Espero que el lector aprecie este artículo y le sea muy útil.

\~ DtxdF
