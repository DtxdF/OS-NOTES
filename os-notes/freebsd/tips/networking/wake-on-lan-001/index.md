# Cómo activar Wake on Lan en FreeBSD

Wake on Lan, o mejor conocido simplemente como WOL, nos permite encender remotamente una computadora que se encuentra apagada.

Esto es verdaderamente útil cuando no tenemos la posibilidad de encender la computadora físicamente, o tengamos una necesidad muy específica referente al tema.

Los requisitos son básicos: tener una placa base compatible y una tarjeta de red igualmente compatible. Para nuestra suerte, la mayoría de estos requisitos están predispuestos por los fabricantes, o al menos en la actualidad.

Para verificar que nuestro controlador sea compatible, podremos usar el siguiente comando y verificar si en la lista está.

```sh
grep -l IFCAP_WOL /usr/src/sys/dev/*/*.c
```

Más información: https://wiki.freebsd.org/Networking/WakeOnLan

El primer paso es activar esta característica desde la BIOS, y el segundo es en la tarjeta de red desde FreeBSD. Para realizar esto necesitamos usar la utilidad ifconfig(8) con privilegios.

```sh
ifconfig IDENTIFICADOR wol
```

Donde IDENTIFICADOR es nuestro identificador de red, como lo puede ser re0

Ahora en otro dispositivo, que quizá podría tener FreeBSD como sistema operativo, se puede encender usando el comando wake:

```sh
wake DIRECCIÓN_MAC
```

Donde DIRECCIÓN_MAC es la dirección física del objetivo.

Para los usuarios de Android, hay una aplicación llamada Wake on Lan: https://play.google.com/store/apps/details?id=co.uk.mrwebb.wakeonlan&hl=es&gl=US

\~ DtxdF