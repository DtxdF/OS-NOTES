# Las jaulas y DHCP

Un poco de búsqueda por la web acerca de las jaulas y DHCP nos revela, al menos desde que VNET fue puesto en producción en FreeBSD, que está la posibilidad de poder usar DHCP para configurar las jaulas sin ningún tipo de *hack*. No obstante, hay algunos usuarios \[**USERS**\] que lamentablemente no tuvieron éxito.

El problema es que se están haciendo supociones incorrectas. Analicemos primero el escenario para ver qué está mal. Primero veamos nuestro archivo de configuración para las jaulas **/etc/jail.conf** y luego el **/etc/rc.conf** que está dentro del entorno aislado con la jaula.

**/etc/jail.conf**

```sh
$r = "/var/jails";
$j = "$r/jail";
path = "$j/$name";

securelevel = 1;

allow.set_hostname = 0;

exec.start = "/bin/sh /etc/rc";
exec.stop = "/bin/sh /etc/rc.shutdown";
exec.clean;
exec.timeout = 60;
exec.consolelog = "$r/log/$name.log";

mount.devfs;

$h = "dtxdf-test";
host.hostname = "$name.$h";

# /etc/defaults/devfs.rules:devfsrules_jail=4
devfs_ruleset = 4;

test_jail {
        # /etc/devfs.rules:devfsrules_dhcp
        devfs_ruleset = 10;
        exec.created = "cpuset -l 1-2,6-7 -j $name";
        vnet;
        vnet.interface = e0b_$name;
        exec.prestart = "jib addm $name jext";
        exec.poststop = "jib destroy $name";
}
```

Prefiero dejar exactamente la jaula de prueba tal cual como la tengo en mi host personal, pero solo explicaré lo más relevante para el problema a solucionar.

En esta jaula se usará el *ruleset* 10, que corresponde a lo siguiente:

```sh
[devfsrules_dhcp=10]
add include $devfsrules_hide_all
add include $devfsrules_unhide_basic
add include $devfsrules_unhide_login
add path 'bpf*' unhide
```

Se creó un nuevo conjunto de reglas para devfs, para asignar lo básico y requerido para mis propósitos, y se habilitó **/dev/bpf** con el objetivo de usar dhclient.

Esta jaula usa vnet para tener un stack propio, el cual se crea con jib \[**JIB**\]. La interfaz a utilizar será el otro par creado por la interfaz virtual if_epair(4), e0b_test_jail, el cual se deberá usar a continuación, con la jaula.

Una vez recopilado los datos a utilizar y puestos los requerimientos en marcha, es necesario iniciar la jaula. Podemos configurar **rc.conf** antes de iniciar la jaula desde la raíz de ella misma en el host o podemos configurarla dentro de ella. Se prefiere configurar fuera de ella antes de iniciarla.

**/var/jails/jail/test_jail/etc/rc.conf**:

```sh
ifconfig_e0b_test_jail="DHCP"
```

Se inicia:

```sh
service jail start test_jail
```

Pero al mismo tiempo en otra terminal se puede observar el log con `tail -f`:

**/var/jails/log/test_jail.log**:

```sh
ELF ldconfig path: /lib /usr/lib /usr/lib/compat
32-bit compatibility ldconfig path: /usr/lib32
Starting Network: lo0 e0b_test_jail.
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=680003<RXCSUM,TXCSUM,LINKSTATE,RXCSUM_IPV6,TXCSUM_IPV6>
        inet6 ::1 prefixlen 128
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
        inet 127.0.0.1 netmask 0xff000000
        groups: lo
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
e0b_test_jail: flags=8863<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8<VLAN_MTU>
        ether 0e:9a:d2:e0:6d:a3
        hwaddr 02:94:68:1e:3c:0b
        groups: epair
        media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
        status: active
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
add host 127.0.0.1: gateway lo0 fib 0: route already in table
add host ::1: gateway lo0 fib 0: route already in table
add net fe80::: gateway ::1
add net ff02::: gateway ::1
add net ::ffff:0.0.0.0: gateway ::1
add net ::0.0.0.0: gateway ::1
Waiting 30s for the default route interface: .............................
Clearing /tmp.
Creating and/or trimming log files.
Updating motd:.
Updating /var/run/os-release done.
Starting syslogd.
Starting sendmail_submit.
Starting sendmail_msp_queue.
Starting cron.

Sat Jul  9 21:10:56 -04 2022
```

No hubo errores, pero tampoco obtuvimos un dato que nos ayude a orientarnos.

Aquí es donde mi cerebro se empezó a distorsionar por saber qué estaba pasando. "*Situaciones extremas, requieren medidas extremas*" A veces este tipo de frases pueden ser divertidas. Dado que no tenía ningún dato más, se me ocurrió depurar las subrutinas de /etc/network.subr y averiguar qué es lo que hace al llegar a *ifconfig_eb0_test_jail="DHCP"*, aparte de depurar /etc/rc.d/dhclient y /etc/rc.d/netif ya que algo tienen que ver con toda esta conspiración

Vamos primero con /etc/network.subr, y adelanto de una vez lo más importante. Líneas 234 a 242:

```sh
        if dhcpif $1; then
            if [ $_cfg -ne 0 ] ; then
                ${IFCONFIG_CMD} $1 up
            fi
            if syncdhcpif $1; then
                /etc/rc.d/dhclient start $1
            fi
            _cfg=0
        fi
```

Dos funciones son las que entran en juego. Dos funciones son las que hay que analizar.

**dhcpif**:

```
# dhcpif if
#       Returns 0 if the interface is a DHCP interface and 1 otherwise.
dhcpif()
{
        local _tmpargs _arg
        _tmpargs=`_ifconfig_getargs $1`

        case $1 in
        lo[0-9]*|\
        stf[0-9]*|\
        lp[0-9]*|\
        sl[0-9]*)
                return 1
                ;;
        esac
        if noafif $1; then
                return 1
        fi

        for _arg in $_tmpargs; do
                case $_arg in
                [Dd][Hh][Cc][Pp])
                        return 0
                        ;;
                [Nn][Oo][Ss][Yy][Nn][Cc][Dd][Hh][Cc][Pp])
                        return 0
                        ;;
                [Ss][Yy][Nn][Cc][Dd][Hh][Cc][Pp])
                        return 0
                        ;;
                esac
        done

        return 1
}
```

**syncdhcpif**:

```sh
# syncdhcpif
#       Returns 0 if the interface should be configured synchronously and
#       1 otherwise.
syncdhcpif()
{
        local _tmpargs _arg
        _tmpargs=`_ifconfig_getargs $1`

        if noafif $1; then
                return 1
        fi

        for _arg in $_tmpargs; do
                case $_arg in
                [Nn][Oo][Ss][Yy][Nn][Cc][Dd][Hh][Cc][Pp])
                        return 1
                        ;;
                [Ss][Yy][Nn][Cc][Dd][Hh][Cc][Pp])
                        return 0
                        ;;
                esac
        done

        checkyesno synchronous_dhclient
}
```

La primera función, dhcpif, acepta valores como DHCP, NOSYNCDHCP y SYNCDHCP. La segunda, syncdhcpif, acepta valores como NOSYNCDHCP y SYNCDHCP. Esto es lo que evalúa y es importante tenerlo presente.

Volviendo a las líneas 234 a 242, dhcpif solo se encarga de verificar que la interfaz está ajustada para ser configurada a través de DHCP siguiendo las reglas que anteriormente vimos en la función misma. La variable **$_cfg** se encarga de verificar que la interfaz no haya sido "tocada", por lo que si es así, la marca como "up" (ver el manual de ifconfig(8)). Luego con syncdhcpif se verifica si está ajustada en los valores que él acepta, si es así, llama a /etc/rc.d/dhclient configurando la interfaz con DHCP.

Apenas 8 líneas pueden y hacen todo eso, y son cruciales, ya que nos responden una pregunta y a su vez nos dejan con una interrogante. Ya sabemos cómo podríamos tener éxito configurando DHCP dentro de la jaula, lo cual es simplemente cambiar DHCP por SYNCDHCP.

```sh
ifconfig_e0b_test_jail="SYNCDHCP"
```

Reiniciamos la jaula y vemos nuevamente el log.

**/var/jails/log/test_jail.log**:

```sh
ELF ldconfig path: /lib /usr/lib /usr/lib/compat
32-bit compatibility ldconfig path: /usr/lib32
Starting dhclient.
DHCPREQUEST on e0b_test_jail to 255.255.255.255 port 67
DHCPACK from 10.0.0.1
bound to 10.0.0.18 -- renewal in 129600 seconds.
Starting Network: lo0 e0b_test_jail.
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=680003<RXCSUM,TXCSUM,LINKSTATE,RXCSUM_IPV6,TXCSUM_IPV6>
        inet6 ::1 prefixlen 128
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
        inet 127.0.0.1 netmask 0xff000000
        groups: lo
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
e0b_test_jail: flags=8863<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8<VLAN_MTU>
        ether 0e:9a:d2:e0:6d:a3
        hwaddr 02:16:a9:f9:68:0b
        inet 10.0.0.18 netmask 0xffffff00 broadcast 10.0.0.255
        groups: epair
        media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
        status: active
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
add host 127.0.0.1: gateway lo0 fib 0: route already in table
add host ::1: gateway lo0 fib 0: route already in table
add net fe80::: gateway ::1
add net ff02::: gateway ::1
add net ::ffff:0.0.0.0: gateway ::1
add net ::0.0.0.0: gateway ::1
Clearing /tmp.
Creating and/or trimming log files.
Updating motd:.
Updating /var/run/os-release done.
Starting syslogd.
Starting sendmail_submit.
Starting sendmail_msp_queue.
Starting cron.

Sat Jul  9 21:59:47 -04 2022
```

Ahora solo nos queda la interrogante...

### La curiosidad le gana al tiempo

Pese a que ya había "arreglado" el problema, y pude conformarme con solo saber lo que anteriormente aprendimos, mi curiosidad pudo más.

Es posible configurar DHCP con solo colocar la palabra clave SYNCDHCP en **/etc/rc.conf**, pero todavía no sabemos por qué la palabra clave **DHCP** no funciona.

Si volvemos a analizar el código anteriormente, la palabra clave DHCP no se encarga directamente de configurar la interfaz usando DHCP, es más una validación, o en otras palabras, permitir que herramientas como dhclient configuren esa interfaz, dado que si no está puesta en su lugar, dhclient se quejará:

```sh
service dhclient start eb0_test_jail
'eb0_test_jail' is not a DHCP-enabled interface
```

Cada vez vamos adquiriendo más información sobre qué hace cada peón en la configuración de red de FreeBSD, pero parece que cada vez tenemos más lejana la respuesta...

### ¿Quién se encarga de configurar DHCP en el host?

Habiendo agotado los análisis en la jaula, pensé que era mejor forma de analizar desde el host, entonces eso me otorgó una nueva pregunta: *¿Quién es el encargado de configurar DHCP en el host?* Dado que las subrutinas de /etc/network.subr no realizan más nada con DHCP al menos que la palabra clave SYNCDHCP se use en lugar de DHCP. Entonces es el momento preciso cuando un vago, pero para nada irrelevante recuerdo de mi memoria vino hacia mí. Entonces ingreso en la jaula y ejecuto lo siguiente:

```sh
devd
devd: Can't open devctl device /dev/devctl: No such file or directory
```

Así es, mi memoria me dio una pista y no falló en ello, el culpable o mejor dicho nuestro malhechor es el querido devd, ver líneas 47 a 58 en **/etc/devd.conf**:

```sh
#
# Try to start dhclient on Ethernet-like interfaces when the link comes
# up.  Only devices that are configured to support DHCP will actually
# run it.  No link down rule exists because dhclient automatically exits
# when the link goes down.
#
notify 0 {
        match "system"          "IFNET";
        match "type"            "LINK_UP";
        media-type              "ethernet";
        action "service dhclient quietstart $subsystem";
};
```

Es por eso que tiene que estar en *up* la interfaz. Es por eso que no se llama a /etc/rc.d/dhclient ya que podría causar un retraso mínimo, pero innecesario al inicio, y sí, devd no es posible ejecutarlo en la jaula al menos que esté apagado en el host, y no, no es recomendable hacerlo en la mayoría de situaciones.

## Lecciones aprendidas

Es importante ejercitar nuestra memoria ya que los datos son imprescindibles para resolver problemas triviales como estos, pero es aun más importante tener supuestos correctos para ir por el camino correcto.

En este caso esos supuestos fueron el hecho de que la jaula tenía los mismos derechos a realizar lo mismo que el host, lo cual es incorrecto, como se pudo observar. Y el dato importante es colocar a devd como otro factor para configurar una interfaz.

Una nota importante más: si se es observador, se puede notar que mencioné a netif en la conspiración, pero resulta que él, pese a ser el que ejecuta gran parte parte de las subrutinas de red, fue irrelevante porque es solo una parte abstracta del problema y es por eso que me enfoqué más en **/etc/network.subr**.

Otra forma de solucionar el problema es usando `exec.start += "dhclient <if>";` en /etc/jail.conf en vez de SYNCDHCP en **/etc/rc.conf**, y funcionaría bien, pero el principal problema es que si no recibimos una respuesta, la jaula creada e iniciada, tendrá que ser eliminada por culpa de dhclient. Esto no puede ser deseable en ciertas situaciones, pero si estás usando DHCP para configurar una interfaz, hay que tomar ese riesgo.

## Referencias

\[**USERS**\] Ver por ejemplo en "Reddit" el usuario TheBypherNinja: "[Cannot start and DHCP Jails](https://www.reddit.com/r/freenas/comments/n850hp/comment/gxgldod)" Armin Moradi en "amoradi.org": "[FreeBSD jails and vnet from scratch, Configuring DHCP and friends](https://web.archive.org/web/20220710003520/https://www.amoradi.org/20210908201936.html)"

\[**JIB**\] Derechos reservados a Devin Teske. jib o *Jail-Interface-Bridge* está ubicado en el sistema base de FreeBSD: **/usr/share/examples/jails/jib**.

\~ DtxdF