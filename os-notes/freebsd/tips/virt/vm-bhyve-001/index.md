Cómo instalar Alpine Linux usando vm-bhyve

Bhyve es un potente hipervisor de tipo 2 escrito para FreeBSD que soporta múltiples invitados. Entre ellos pueden ser: FreeBSD, OpenBSD, NetBSD, Windows, Ubuntu, Alpine, Debian, DragonflyBSD, Arch Linux, y un largo etcétera.

Según la misma documentación de FreeBSD, bhyve requiere Extended Page Tables (EPT) o AMD Rapid Virtualization Indexing (RVI) o Nested Page Tables (NPT).

Más información: https://docs.freebsd.org/en/books/handbook/virtualization/#virtualization-host-bhyve

En este modesto tutorial se usará vm-bhyve como administrador de máquinas virtuales, siendo una opción bastante potente y fácil de usar y configurar.

Comenzamos, pero con privilegios, instalándolo:

```sh
pkg install vm-bhyve
```

A su vez, como Alpine requiere grub, necesitamos instalarlo:

```sh
pkg install grub2-bhyve
```

Además, que si queremos facilitar el proceso de configuración de red, debemos instalar un servidor DHCP y DNS:

```sh
pkg install dnsmasq
```

Ahora pasamos directamente con la configuración de vm-bhyve y la de red.

```sh
sysrc gateway_enable="YES"
sysrc pf_enable="YES"
sysrc vm_enable="YES"
sysrc dnsmasq_enable="YES"

# Aquí depende del nombre del pool. En mi caso es zroot
mkdir /var/vm
zfs create zroot/var/vm
sysrc vm_dir="zfs:zroot/var/vm"
vm init

# Copiamos la plantilla de alpine
cp -va /usr/local/share/examples/vm-bhyve/alpine.conf /var/vm/.templates
sed 's/vanilla/lts/g;w /var/vm/.templates/alpine.conf.1'
mv /var/vm/.templates/alpine.conf.1 /var/vm/.templates/alpine.conf

# Ahora necesitamos realizar NAT. Las Xs son los octetos, o sea, la dirección IP de nuestra interfaz pública. vtnet0 es mi interfaz, pero coloquen la suya. La red es mi dirección IP privada, y el CIDR lo pueden calcular o, ya sea manual, o con ipcalc.
echo nat pass on vtnet0 inet from 10.10.0.1/16 to any -> x.x.x.x >> /etc/pf.conf
vm switch create -a 10.10.0.1/16 public
sysrc defaultrouter='10.10.0.1/16'
systl net.inet.ip.forwarding=1
service pf start

cat << 'EOF' >> /usr/local/etc/dnsmasq.conf
server=208.67.222.222
server=208.67.220.220
domain-needed
no-resolv
except-interface=lo0
dhcp-range=10.10.0.2,10.10.0.254
EOF
service dnsmasq start

vm iso https://dl-cdn.alpinelinux.org/alpine/v3.1/releases/x86_64/alpine-standard-3.13.5-x86_64.iso
vm create -t alpine alpine
vm install -f alpine alpine-standard-3.13.5-x86_64.iso
```

Hay múltiples formas de averiguar una correcta nomenclatura para la plantilla que se usará. En mi caso tuve que cambiar vanilla por lts. Es recomendable el siguiente artículo: https://github.com/churchers/vm-bhyve/wiki/Guest-example:-Alpine-Linux

También, si se desea una mejor optimización, se puede escoger la imagen para máquinas virtuales.

Ahora, para instalar alpine, es recomendable leer la documentación oficial para proseguir:

https://docs.alpinelinux.org/user-handbook/0.1a/Installing/setup_alpine.html

\~ DtxdF