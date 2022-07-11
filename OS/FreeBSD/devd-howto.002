# Cómo usar DEVD

devd(8) es una herramienta que viene incluida en el sistema base de FreeBSD. Esta pieza de ingeniería provee una forma para que las herramientas de usuario se ejecuten cuando determinados eventos del kernel sucedan.

¿Quieres llamar a dhclient cuando una interfaz esté en "up"? devd(8) puede hacerlo por ti. ¿Quieres montar un sistema de archivos fat32 cuando se conecte determinado USB? devd(8) puede hacerlo por ti. devd(8) puede hacer todo lo que desees cuando determinado evento suceda.

## Archivo de configuración de devd(8)

El archivo de configuración de devd(8), documentado en **devd.conf(5)**, nos permite definir reglas a nuestro gusto. En el sistema base ya viene incluido uno, que es `/etc/devd.conf`, del cual no se debería tocar, ya que si se hace una actualización, tendríamos que resolver conflictos y remodificar, algo un poco tedioso, más cuando son varios servidores o PCs. Por suerte, hay otra forma, mejor y más organizada de escribir este archivo de configuración, siendo este a través del directorio `/usr/local/etc/devd`. Con este directorio, es posible colocar cada archivo, con la extensión .conf, y crear reglas para diferentes propósitos.

Si se ha instalado algún programa que utilice devd(8), es probable que ya se tenga ese directorio, de lo contrario, es necesario crearlo.

```sh
mkdir -p /usr/local/etc/devd
```

Una configuración consiste en dos características, sentencias y comentarios. Todas las sentencias terminan con un punto y coma. Muchas sentencias contienen sub-sentencias, las cuales, y de igual forma, terminan con punto y coma. Por ejemplo:

```
statement priority {
    substatement "value";
    ...
    substatement "value";
};
```

Las sentencias pueden ocurrir en cualquier orden en el archivo de configuración, y pueden ser repetidas tan a menudo como sea requerido.

Cada sentencia tiene una prioridad, a excepción de las opciones (ver más abajo). La prioridad es un número arbitrario, siendo el más bajo "0", por lo tanto, con menos prioridad. Si dos sentencias tienen la misma coincidencia, la que tenga mayor prioridad, gana.

Las sentencias actualmente soportadas serían las siguientes:

* `attach`: Cuando un dispositivo es conectado y coincide con cierto criterio, ejecuta una acción determinada;
* `detach`: Lo contrario a la anterior, o en otras palabras, realiza una acción determinada cuando un dispositivo es desconectado y coincide con un criterio determinado;
* `nomatch`: Esta sentencia es desencadenada cuando un nuevo dispositivo no coincide con ningún controlador;
* `notify`: Esta opción puede ser la más útil en la mayoría de circunstancias, debido a que nos permite realizar acciones cuando el kernel envía un evento al espacio de usuario. Normalmente podemos apreciar esta clase de mensajes en `/var/log/messages` o ejecutando `dmesg(8)`;
* `options`: Especifica varias opciones y parámetros que podemos utilizar a lo largo de las sentencias que creemos.

Una forma de comprender las sentencias es leer directamente `/etc/devd.conf` y repasar lo anteriormente indicado. También podría ser útil leer lo que se encuentra en el directorio `/etc/devd` y si un software que tengamos instalado ha creado `/usr/local/etc/devd`, es de igual forma un buen punto de partida.

### Sub-sentencias

Cada sub-sentencia varía dependiendo de la sentencia, es por eso que se devidirá en cinco secciones, dado que son cinco sentencias.

#### options

* `directory "/some/path";`: Le indica a devd(8) nuevos directorios para leer e interpretar nuevas sentencias con cualquier archivo que tenga como extensión ".conf". Si se es observador, `/etc/devd.conf` ya tiene dos de estas sub-sentencias en `options` para agregar a los directorios `/etc/devd` y `/usr/local/etc/devd`;
* `pid-file "/var/run/devd.pid";`: Indica el archivo donde se almacenará el ID del proceso (PID, en inglés). De nuevo en `/etc/devd.conf` ya está definida una sub-sentencia para hacer esto;
* `set regexp-name "(some|regexp)";`: Crea una expresión regular la cual puede ser usada para detonar una coincidencia. Si comienza con "!", coincide si la expresión regular no coincide con el criterio. Todas las expresiones regulares tiene implícito "$^" alrededor de ellas. En `/etc/devd.conf` hay expresiones regulares tal como `scsi-controller-regex` y `wifi-driver-regex`, que sirven como ejemplo, las cuales son referenciadas con el signo de dólar en algunas sub-sentencias, como por ejemplo, `device-name "$wifi-driver-regex";`.

#### attach y detach

* `action "command";`: Ejecuta un comando cuando haya una coincidencia. Por ejemplo `/etc/pccard_ether $device-name start`;
* `class "string";`: Es un atajo a `match "class" "string"`;
* `device-name "string";`: Es un atajo `match "device-name" "string"`. Cuando hay una coincidencia, también se crea una variable `$device-name` la cual puede ser usada en una sub-sentencia como `action`;
* `match "variable" "value";`: Coincide con el contenido de `value` contra la variable. Por ejemplo, `match "device-name" "$wifi-driver-regex";` debe coincidir con el nombre del dispositivo según la expresión regular `$wifi-driver-regex`;
* `media-type "string";`: Para dispositivos de red, `media-type` coincidirá los dispositivos con los siguientes tipos: `Ethernet`, `Tokenring`, `FDDI`, `802.11`, y `ATM`.
* `subdevice "string"`: Es un atajo a `match "subdevice" "string";`.

#### nomatch

Las siguientes sub-sentencias tienen el mismo efecto que `attach` y `detach`, por lo que solo se nombrarán sin dar explicación.

* `action "command";`;
* `match "variable" "value";`.

#### match

Las sub-sentencias que tengan el mismo efecto que las anteriores no serán descritas. La excepción será, como verán, `action`, pero es debido a que se brinda un ejemplo para tener una idea de qué herramienta sería útil llamar en esta sub-sentencia.

* `action "command";`: Ejecuta un comando cuando haya una coincidencia. Por ejemplo `action "service dhclient quietstart $subsystem"`. Otro ejemplo útil es cuando se requiere usar la variable `$notify` que es relativa al sistema y subsistema que ha desencadena el evento: `/etc/rc.d/power_profile $notify`;
* `match "system | subsystem | type | notify" "value";`: Un número arbitrario de sub-sentencias `match` puede existir en una sentencia `match`, y como los demás sub-sentencias `match`, `value` puede ser un valor fijo o una expresión regular.
* `media-type "string";`.

### Variables y el evento Notify

Hay un número considerable de variables, sistemas, subsistemas, tipos y notificaciones que se pueden usar en las sentencias que usen la sub-sentencia`match`, pero que simplemente no colocaré porque el manual **devd.conf(5)** ya proporciona de forma detallada. Ver secciones `Variables that can be used with the match statement` y `Notify matching` en el manual **devd.conf(5)**.

### Comentarios

Los comentarios son triviales para los programadores de C, C++ o Shell/Perl.

El estilo C comienza con dos caracteres "/*" (barra diagonal, asterisco) y terminan con "*/" (asterisco, barra diagonal). Puede cubrir una sola línea o múltiples líneas. Los comentarios de este estilo no pueden ser anidados.

```C
/* This is the start of a comment.
    This is still part of the comment.
/* This is an incorrect attempt at nesting a comment. */
    This is no longer in any comment. */
```

El estilo C++ inicia con dos caracteres "//" (barra diagonal, barra diagonal) y solo pueden cubrir una línea. Si se desea cubrir más de una, es necesario colocar este tipo de comentarios por cada línea:

```C++
// This is the start of a comment.  The next line
// is a new comment, even though it is logically
// part of the previous comment.
```

### Notas cuando una variable es expandida

Para prevenir incidentes con caracteres especiales de la shell, lo siguiente es lo que sucede con una variable, digamos, `$foo`:

1. Los caracteres "$'" son insertados.
2. La cadena "$foo" es eliminada.
3. El valor de la variable foo se inserta en el buffer con todos los caracteres de comillas simples precedidos por una barra invertida.

En todos los contextos esas condiciones son seguras, pero con las comillas simples, puede producir efectos indeseados. Suponiendo que foo=meta y bar=var, entonces lo siguiente:

```
action "echo '$foo $bar'";
```

Será presentado a la shell a través de la función system(3):

```sh
echo '$'meta' $'var''
```

Lo cual produce como salida:

```sh
echo $meta $var
```

¿No se deseaba *$meta* y *$var* o salida está bien? No creo que sea lo que se deseaba. Para que se produzca una salida correctamente, es necesario reescribir la regla de la siguiente forma:

```sh
action "echo $foo' '$bar"
```

Atentos con eso.

## devd(8) en modo depuración

Por defecto devd(8) se ejecuta como un servicio más en nuestro sistema:

```sh
# service devd status
devd is running as pid 3514.
```

Pero para conocer mejor qué hacer es necesario ver lo que hace en plena ejecución con los detalles implicados. Como ya se aclaro, muchos eventos se pueden ver usando herramientas como dmesg(8) o leer `/var/log/messages`, pero combinarlas con `devd -d` en modo depuración es más efectivo, pero hace necesario entonces, mientras desarrollamos una nueva regla, que apaguemos el servicio devd que está en ejecución y pasemos a ejecutar devd en primer plano o modo depuración.

```sh
# service devd stop
Stopping devd.
Waiting for PIDS: 3825.
# devd -d
Parsing /etc/devd.conf
setting scsi-controller-regex=(aac|aacraid|ahc|ahd|amr|ciss|esp|ida|iir|ips|isp|mlx|mly|mpr|mps|mpt|sym|trm)[0-9]+
setting wifi-driver-regex=(ath|bwi|bwn|ipw|iwi|iwm|iwn|malo|mwl|otus|ral|rsu|rtwn|rum|run|uath|upgt|ural|urtw|wi|wpi|wtap|zyd)[0-9]+
Parsing files in /etc/devd
Parsing /etc/devd/uath.conf
Parsing /etc/devd/hyperv.conf
Parsing /etc/devd/zfs.conf
Parsing /etc/devd/iwmbtfw.conf
Parsing /etc/devd/asus.conf
Parsing /etc/devd/devmatch.conf
Parsing /etc/devd/ulpt.conf
Parsing files in /usr/local/etc/devd
Parsing /usr/local/etc/devd/cups.conf
```

La salida es bien descriptiva. Nos muestra lo que está parseando, lo cual puede ser útil para depuración. Algunas veces con la salida de dmesg(8) es suficiente para resolver nuestros problemas, pero recomiendo usar `devd -d` mientras se crea una regla para saber si la regla coincide con un evento o no.

A modo de ejemplo conectaré un dispositivo de almacenamiento USB y veré la salida de dmesg(8) después de haberlo conectado:

```
umass0 on uhub0
umass0: <SanDisk Cruzer Blade, class 0/0, rev 2.00/1.27, addr 2> on usbus4
umass0:  SCSI over Bulk-Only; quirks = 0x8100
umass0:2:0: Attached to scbus2
da0 at umass-sim0 bus 0 scbus2 target 0 lun 0
da0: <SanDisk Cruzer Blade 1.27> Removable Direct Access SPC-4 SCSI device
da0: Serial Number 200515364102BCF2AC69
da0: 40.000MB/s transfers
da0: 7633MB (15633408 512 byte sectors)
da0: quirks=0x2<NO_6_BYTE>
```

Tenemos información suficiente como para crear sentencias. Crearemos `usb_attach.conf` en `/usr/local/etc/devd` a modo ilustrativo.

**/usr/local/etc/devd/usb_attach.conf**:

```
attach 100 {
        device-name "umass0";
        action "logger $device-name is plugged in";
};

detach 100 {
        device-name "umass0";
        action "logger $device-name is unplugged";
};
```

Al conectar un dispositivo que coincida con `umass0`, imprimirá su nombre usando logger. Podemos ver los mensajes desde `/var/log/messages`.

```
Jul 11 15:57:01 dtxdf-laptop dtxdf-fbsd[4832]: umass0 is unplugged
Jul 11 15:57:01 dtxdf-laptop kernel: ugen4.2: <SanDisk Cruzer Blade> at usbus4 (disconnected)
Jul 11 15:57:01 dtxdf-laptop kernel: umass0: at uhub0, port 2, addr 2 (disconnected)
Jul 11 15:57:01 dtxdf-laptop kernel: da0 at umass-sim0 bus 0 scbus2 target 0 lun 0
Jul 11 15:57:01 dtxdf-laptop kernel: da0: <SanDisk Cruzer Blade 1.27>  s/n 200515364102BCF2AC69 detached
Jul 11 15:57:01 dtxdf-laptop kernel: (da0:umass-sim0:0:0:0): Periph destroyed
Jul 11 15:57:01 dtxdf-laptop kernel: umass0: detached
Jul 11 15:57:07 dtxdf-laptop dtxdf-fbsd[4836]: umass0 is plugged in
Jul 11 15:57:07 dtxdf-laptop kernel: ugen4.2: <SanDisk Cruzer Blade> at usbus4
Jul 11 15:57:07 dtxdf-laptop kernel: umass0 on uhub0
Jul 11 15:57:07 dtxdf-laptop kernel: umass0: <SanDisk Cruzer Blade, class 0/0, rev 2.00/1.27, addr 2> on usbus4
Jul 11 15:57:07 dtxdf-laptop kernel: umass0:  SCSI over Bulk-Only; quirks = 0x8100
Jul 11 15:57:07 dtxdf-laptop kernel: umass0:2:0: Attached to scbus2
Jul 11 15:57:08 dtxdf-laptop kernel: da0 at umass-sim0 bus 0 scbus2 target 0 lun 0
Jul 11 15:57:08 dtxdf-laptop kernel: da0: <SanDisk Cruzer Blade 1.27> Removable Direct Access SPC-4 SCSI device
Jul 11 15:57:08 dtxdf-laptop kernel: da0: Serial Number 200515364102BCF2AC69
Jul 11 15:57:08 dtxdf-laptop kernel: da0: 40.000MB/s transfers
Jul 11 15:57:08 dtxdf-laptop kernel: da0: 7633MB (15633408 512 byte sectors)
Jul 11 15:57:08 dtxdf-laptop kernel: da0: quirks=0x2<NO_6_BYTE>
```

En mi caso ya lo tenía conectado.

Pero este ejemplo se puede extender a un caso de uso más realista, como montar una partición apenas lo conectemos, aunque esto supone un reto más, no complicado claro está, pero se hace evidente que es requerido saber cómo se llamará el dispositivo o partición que montaremos en nuestro sistema de archivos. Es importante notar que los dispositivos `/dev/da[0-9]+` no simplifican nuestra labor, de hecho, la complican porque puede que tengamos varios dispositivos de almacenamiento USB con distintos sistemas de archivos, con diferentes esquemas de particiones o incluso diferentes particiones.

Hay una forma muy simple pero efectiva de solucionar este inconveniente, y es simplemente identificar las particiones de forma única desde un principio. No es necesario crear un identificador para la partición único para el mundo entero, es suficiente con que sea único para nuestro entorno. Una forma efectiva de hacerlo es conociendo cómo están nombradas las particiones de otros dispositivos de nuestro entorno, lo cual es sencillo si se tiene en la mano el inventario, pero si no, también hay una solución gracias a FreeBSD, o mejor dicho, diskinfo(8).

Con diskinfo(8) podemos obtener el identificador o el número serial de nuestro dispositivo usando el parámetro "-s":

```sh
$ diksinfo -s da0
200515364102BCF2AC69
```

El número serial es muy largo, por lo que usar los últimos cinco caracteres sería buena idea, aunque elige la forma que se adapte a tu entorno. Aparte de eso me gusta concatenar una abreviatura de lo que significa esa partición con la parte del número serial. Dado que para este ejercicio lo quiero hacer simple solo usaré una partición, la cual será la principal o la raíz para almacenar datos. El resultado sería el siguiente:

```
rt2AC69
```

Con eso podemos particionar.

```sh
# gpart create -s gpt da0
da0 created
# gpart add -t freebsd-ufs -l rt2AC69 da0
da0p1 added
# newfs -j /dev/gpt/rt2AC69
/dev/gpt/rt2AC69: 7633.5MB (15633328 sectors) block size 32768, fragment size 4096
        using 13 cylinder groups of 625.22MB, 20007 blks, 80128 inodes.
        with soft updates
super-block backups (for fsck_ffs -b #) at:
 192, 1280640, 2561088, 3841536, 5121984, 6402432, 7682880, 8963328, 10243776, 11524224, 12804672, 14085120, 15365568
Using inode 4 in cg 0 for 33554432 byte journal
newfs: soft updates journaling set
# mount /dev/gpt/rt2AC69 /mnt
# echo "Hello!" > /mnt/hello.txt
# cat /mnt/hello.txt
Hello!
# umount /mnt
```

Usamos GPT como nuestro esquema de particiones, y aprovechamos que GPT puede identificar nuestra partición de forma arbitraria. Gracias a GPT también tenemos otro identificador en /dev/gptid, pero para los humanos es increiblemente difícil manejarlos, por lo que es mejor crear uno propio como se hizo aquí.

Ahora que hemos identificado la partición, desconectemos el dispositivo de almacenamiento USB y volvamos a conectarlo. El identificador de la partición no solo será útil para la acción que crearemos, lo es también para ayudarnos mientras depuramos con devd(8).

```
Processing event '!system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69'
Pushing table
setting *=!system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69
setting _=system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69
setting timestamp=1657571585.404812
setting system=GEOM
setting subsystem=DEV
setting type=CREATE
setting cdev=gpt/rt2AC69
Processing notify event
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^ETHERNET$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^HYPERV_NIC_VF$, invert=0
Testing system=GEOM against ^ETHERNET$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^ZFS$, invert=0
Testing system=GEOM against ^IFNET$, invert=0
Testing system=GEOM against ^IFNET$, invert=0
Testing system=GEOM against ^IFNET$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Testing system=GEOM against ^ACPI$, invert=0
Popping table
```

Cuando conectamos nuestro USB, la salida de devd(8) es impresionantemente larga, lo cual es útil, pero abrumador si se es primerizo, no obstante recomiendo herramientas como tmux o gnu-screen que nos ayudarían en estos casos cuando los ponemos en *Copy Mode*, lo cual nos permite buscar a través del buffer de salida. Si no queremos, o no podemos instalar herramientas de terceros tenemos la posibilidad de usar script(1) con un paginador como more(1) o less(1).

La idea de indagar en la salida de devd(8) es usar el identificador de la partición para obtener el punto exacto del evento que necesitamos. Ya habiendo hecho eso, necesitamos saber si los parámetros que está usando devd(8) son los que requerimos, en mi caso, los relevantes serán:

```
setting system=GEOM
setting subsystem=DEV
setting type=CREATE
setting cdev=gpt/rt2AC69
```

Para saber qué significa cada uno, recomiendo leer la sección "**Variables y el evento Notify**" anteriormente descrita.

Ahora es sencillo modificar nuestro `usb_attach.conf`:

```
notify 100 {
        match "system" "GEOM";
        match "subsystem" "DEV";
        match "type" "CREATE";
        match "cdev" "gpt/rt2AC69";
        action "sleep 2 && /usr/local/bin/scripts/automount.sh $cdev";
};

notify 100 {
        match "system" "GEOM";
        match "subsystem" "DEV";
        match "type" "DESTROY";
        match "cdev" "gpt/rt2AC69";
        action "sleep 2 && /usr/local/bin/scripts/autoumount.sh $cdev";
};
```

Muchas veces necesitamos realizar una operación, y luego, su contraparte, por lo que en este caso estamos usando dos scripts: uno para cuando el proveedor aparezca en nuestro sistema y otro para cuando sea destruido. El de destrucción es solo para desmontar forzadamente el sistema de archivos y eliminar la carpeta que se crea en `automount.sh`. Desmontar el sistema de archivos parece una mala idea, pero dado que el dispositivo fue desconectado sin haber desmontado antes el sistema de archivos, se hace necesario.

Los scripts son los siguientes:

**automount.sh**:

```
#!/bin/sh

set -e

: ${RTDIR:=/mnt}

dev=$1
if [ -z "${dev}" ]; then
	echo "usage: automount.sh device" >&2
	exit 1
fi

_dev=`basename "${dev}"`
mntdir="${RTDIR}/${_dev}"

fsck -p "/dev/${dev}"
mkdir -p "${mntdir}"
mount "/dev/${dev}" "${mntdir}"
```

**autoumount.sh**:

```
#!/bin/sh

: ${RTDIR:=/mnt}

dev=$1
if [ -z "${dev}" ]; then
	echo "usage: autoumount.sh device" >&2
	exit 1
fi

_dev=`basename "${dev}"`
mntdir="${RTDIR}/${_dev}"

umount -f "${mntdir}" 2> /dev/null
rmdir "${mntdir}"
```

Al conectar nuestro dispositivo de almacenamiento USB, se montará.

```
Processing event '!system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69'
Pushing table
setting *=!system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69
setting _=system=GEOM subsystem=DEV type=CREATE cdev=gpt/rt2AC69
setting timestamp=1657575727.806329
setting system=GEOM
setting subsystem=DEV
setting type=CREATE
setting cdev=gpt/rt2AC69
Processing notify event
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^DEVFS$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^USB$, invert=0
Testing system=GEOM against ^GEOM$, invert=0
Testing subsystem=DEV against ^DEV$, invert=0
Testing type=CREATE against ^CREATE$, invert=0
Testing cdev=gpt/rt2AC69 against ^gpt/rt2AC69$, invert=0
Executing 'sleep 2 && /usr/local/bin/scripts/automount.sh $'gpt/rt2AC69''
/dev/gpt/rt2AC69: FILE SYSTEM CLEAN; SKIPPING CHECKS
/dev/gpt/rt2AC69: clean, 1880626 free (26 frags, 235075 blocks, 0.0% fragmentation)
Popping table
```

```sh
cat /mnt/rt2AC69/hello.txt
Hello!
```

### No reinventes la rueda

Acabamos de hacer todo lo contrario a la frase o regla "*no reinventar la rueda*" dado que ya existe una implementación de FreeBSD llamada automountd(8), pero para fines didácticos, y en mi humilde opinión, fue una perfecta forma de comprender devd(8).

No obstante, en este caso no me estoy refiriendo a lo que acabamos de realizar, sino que hay veces en que queremos implementar una nueva regla con devd(8) y resulta que, o ya está implementada, o puede servirnos cambiando pocas cosas.

Un ejemplo es usando *USB tethering* desde un dispositivo con Android el cual crea una interfaz urndis(4) la cual se puede configurar con dhclient(8). Si conectamos nuestro dispositivo con Android en ese modo y observamos lo que hace devd(8), validaremos lo que acabo de indicar en los anteriores párrafos:

```
Pushing table
setting *=!system=IFNET subsystem=ue0 type=ATTACH
setting _=system=IFNET subsystem=ue0 type=ATTACH
setting timestamp=1657576470.672852
setting system=IFNET
setting subsystem=ue0
setting type=ATTACH
Processing notify event
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^USB$, invert=0
Testing system=IFNET against ^GEOM$, invert=0
Testing system=IFNET against ^GEOM$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^ACPI$, invert=0
Testing system=IFNET against ^ACPI$, invert=0
Testing system=IFNET against ^ACPI$, invert=0
Testing system=IFNET against ^ACPI$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^DEVFS$, invert=0
Testing system=IFNET against ^HYPERV_NIC_VF$, invert=0
Testing system=IFNET against ^ETHERNET$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^ZFS$, invert=0
Testing system=IFNET against ^IFNET$, invert=0
Testing subsystem=ue0 against ^(usbus|wlan)[0-9]+$, invert=1
Testing type=ATTACH against ^ATTACH$, invert=0
Executing '/etc/pccard_ether $'ue0' start'
Starting Network: ue0.
ue0: flags=8802<BROADCAST,SIMPLEX,MULTICAST> metric 0 mtu 1500
        ether 00:00:00:00:00:00
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
Popping table
Processing event '!system=ETHERNET subsystem=ue0 type=IFATTACH'
Pushing table
setting *=!system=ETHERNET subsystem=ue0 type=IFATTACH
setting _=system=ETHERNET subsystem=ue0 type=IFATTACH
setting timestamp=1657576471.170142
setting system=ETHERNET
setting subsystem=ue0
setting type=IFATTACH
Processing notify event
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^GEOM$, invert=0
Testing system=ETHERNET against ^GEOM$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^ACPI$, invert=0
Testing system=ETHERNET against ^ACPI$, invert=0
Testing system=ETHERNET against ^ACPI$, invert=0
Testing system=ETHERNET against ^ACPI$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^HYPERV_NIC_VF$, invert=0
Testing system=ETHERNET against ^ETHERNET$, invert=0
Testing type=IFATTACH against ^IFATTACH$, invert=0
Executing '/usr/libexec/hyperv/hyperv_vfattach $'ue0' 0'
Popping table
```

El mismo nombre de la herramienta nos da una pista, pero dado que no todo el tiempo la tendremos tan fácil, una manera efectiva de indagar cuál archivo de configuración ejecuta **hyperv_vfattach** es simplemente usando grep(1):

```sh
$ egrep -r '/usr/libexec/hyperv/hyperv_vfattach' /etc/devd
/etc/devd/hyperv.conf:  action "/usr/libexec/hyperv/hyperv_vfattach $subsystem 0";
```

Por supuesto, el orden que debemos indagar es en `/etc/devd.conf`, y luego en sus directorios especificados en la sentencia `options`.

**/etc/devd/hyperv.conf**:

```
notify 10 {
        match "system"          "ETHERNET";
        match "type"            "IFATTACH";
        action "/usr/libexec/hyperv/hyperv_vfattach $subsystem 0";
};
```

Siguiendo la salida podemos obtener exactamente la sentencia que está coincidiendo, ahora es trivial crear una nueva:

**/usr/local/etc/devd/usb_tethering.conf**:

```
notify 20 {
        match "system"          "ETHERNET";
        match "type"            "IFATTACH";
        match "subsystem"       "ue[0-9]+";
        action "/sbin/dhclient $subsystem";
};
```

Aumentamos la prioridad y de una vez le agregamos el subsistema que corresponde con la interfaz urndis(4). También colocamos dhclient(8) en la acción y nuestro dispositivo, al conectarlo en anclaje USB, debería brindarnos conexión a Internet automáticamente.

```
Processing event '!system=ETHERNET subsystem=ue0 type=IFATTACH'
Pushing table
setting *=!system=ETHERNET subsystem=ue0 type=IFATTACH
setting _=system=ETHERNET subsystem=ue0 type=IFATTACH
setting timestamp=1657577216.269419
setting system=ETHERNET
setting subsystem=ue0
setting type=IFATTACH
Processing notify event
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^DEVFS$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^USB$, invert=0
Testing system=ETHERNET against ^GEOM$, invert=0
Testing system=ETHERNET against ^GEOM$, invert=0
Testing system=ETHERNET against ^ETHERNET$, invert=0
Testing type=IFATTACH against ^IFATTACH$, invert=0
Testing subsystem=ue0 against ^ue[0-9]+$, invert=0
Executing '/sbin/dhclient $'ue0''
DHCPDISCOVER on ue0 to 255.255.255.255 port 67 interval 8
DHCPOFFER from 192.168.42.129
DHCPREQUEST on ue0 to 255.255.255.255 port 67
DHCPACK from 192.168.42.129
bound to 192.168.42.2 -- renewal in 1800 seconds.
Popping table
```

## Conclusión

Ya sabemos usar y configurar devd(8), ahora controlar los dispositivos y eventos del sistema es una tarea trivial.

Recomiendo leer las páginas del manual de todas estas herramientas, y aunque no lo mencioné, recomiendo leer devctl(8) también.

\~ DtxdF