# Cómo montar un sistema de archivos de un dispositivo MTP (como Android)

El protocolo de transferencias de medios (MTP, por sus siglas en inglés) es una extensión de PTP que permite transferir archivos desde dispositivos portatiles, tales como cámaras digitales, o para nuestro caso, un teléfono inteligente con Android instalado.

Para más información, leer MTP (https://en.wikipedia.org/wiki/Media_Transfer_Protocol) en Wikipedia.

En FreeBSD tenemos alternativas que podremos instalar con mucho gusto, pero para este pequeño tutorial se usará jmtpfs, la cual se pude instalar con el siguiente comando:

```sh
doas su # Nos autenticamos como superusuarios
pkg install -y fusefs-jmtpfs
```

Ya instalado hemos de proceder a conectar el dispositivo, y dejándolo desbloqueado para que nos muestre el dialogo de confirmación para que nos permita montar el sistema de archivos (ya que tenemos que dar nuestro consentimiento explícito).

Ahora, y una vez conectado, ejecutamos el siguiente comando para listar los dispositivos MTP.

```sh
jmtpfs -l
```

El resultado puede ser más o menos algo así (con un dispositivo conectado):

```
Device 0 (VID=04e8 and PID=6860) is a Samsung Galaxy models (MTP).
Available devices (busLocation, devNum, productId, vendorId, product, vendor):
4, 2, 0x6860, 0x04e8, Galaxy models (MTP), Samsung
```

Si se es atento, nos muestra un número del bus, y un número de dispositivo, el cual deberemos tener a mano para poder montarlo.

```sh
jmtpfs -device=4,2 /mnt
```

Montaremos el sistema de archivos en /mnt, especificando el dispositivo.

Aunque lo anterior solo es útil si contamos con múltiples dispositivos MTP, pero si solo contamos con uno, es aun más fácil, ya que ejecutaríamos un comando sin preocuparnos por la dirección:

```sh
jmtpfs /mnt
```

Ahora se listan los archivos montados:

```sh
ls /mnt
# La salida:
# Card    Phone
```

Hay opciones que nos pueden ser verdaderamente útiles, como violar la política de acceso estricto (ver mount_fsusefs(8)) para poder permitir que otros usuarios no privilegiados pueden interactuar con los archivos.

```sh
jmtpfs -o allow_other /mnt
```

Ver también:

* `jmtpfs -h |& less`

\~ DtxdF
