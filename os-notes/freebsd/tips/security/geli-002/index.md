# Cifrado de disco con GELI

GELI es una utilidad para configurar el cifrado en los proveedores GEOM. Entre sus cualidades son:

* Utiliza el framework crypto(8), entonces cuando hay crypto hardware,
 geli lo usa automáticamente, incrementando así el rendimiento;
* Soporta varios algoritmos criptográficos, siendo éstos actualmente: 
AES-XTS, AES-CBC, Camellia-CBC;
* Puede, aunque opcionalmente, realizar autenticación de los datos o básicamente integridad de los datos, utilizando uno de los siguientes algoritmos: HMAC/SHA1, HMAC/RIPEMD160, HMAC/SHA256, HMAC/SHA384 o HMAC/SHA512;
* Brinda la posibilidad de crear un proveedor con una clave maestra aleatoria que puede ser usada una vez, lo cual puede ser verdaderamente útil sin estamos cifrando el swap o algún sistema de archivos temporal;
* Facilita usar dos y no solo una clave como componentes, las cuales pueden ser, por ejemplo, la clave de la compañía y la clave personal.

Tiene características muy sublimes, y sencillas de recordar, pero es mucho mejor ponerlo en práctica para consolidar las palabras 
anteriormente dichas.

El dispositivo a usar será da0, una simple llave USB. Aunque antes de seguir, es necesario cargar el módulo geom_eli, y si es de agrado, colocar la siguiente línea en /boot/loader.conf.

```sh
geom_eli_load="YES"
```

Es recomendable ser organizados para lo que sea, y no mucho menos con la estructura donde se montará la unidad y donde colocaremos la clave.

```sh
mkdir -p "/media/`diskinfo -s da0`"/{data,key}
```

El comando diskinfo con el parámetro -s retornará la identidad del dispositivo, que usualmente es el número serial.

Por comodidad para lo que haremos a futuro próximo, no es recomendable escribir la clave maestra en el disco si somos muy paranoicos, sino más bien escribirla en la memoria. Para realizar la proposición antes dicha se deberá usar mdconfig.

```sh
device=`mdconfig -a -t malloc -o reserve -s 1m`
newfs -n /dev/$device
mount /dev/$device /mnt
cd /mnt
```

Ya habiendo realizado el proceso dictado con aterioridad, ahora  anexaremos a otro, que es básicamente crear la clave maestra.

```sh
dd if=/dev/random of=`diskinfo -s da0`.key count=1 bs=64
```

64 bytes implican 512 bits, así que, mientras más bits se hallen, más tiempo de CPU implicará.

Es momento de escribir la clave maestra en nuestro disco, pero no tal cual, sino que usaremos Age para cifrarla a su vez.

```sh
age --passphrase --armor -o `diskinfo -s da0`.key `diskinfo -s da0`.key.age
```

Ingresamos una contraseña, y la salida, que será un archivo con extensión .age, la moveremos a un lugar más apropiado en nuestro disco.

```sh
mv `diskinfo -s da0`.key.age /media/`diskinfo -s da0`/key
```

El siguiente paso es inicializar el proveedor.

```
age -d `diskinfo -s da0`.key.age | geli init -K - -s 4096 /dev/da0 -a HMAC/SHA256 /dev/da0
```

Nos preguntará la frase de contraseña primero de Age, luego dos frases de contraseña de geli.

Listos con lo anterior, vamos con lo siguiente: crear el nuevo proveedor que cifrará todo lo que escribamos.

```sh
age -d /media/`diskinfo -s da0`/key/`diskinfo -s da0`.key.age | geli attach -k - /dev/da0
```

Si pillamos la salida del comando ls.

```sh
ls /dev/da0*
/dev/da0        /dev/da0.eli
```

La extensión .eli nos indica que ya hemos logrado la mayor parte. Es posible usarlo, aunque no sin antes haberlo formateado, pero es altamente recomendable llenar de basura cualquier dato que se encuentre en el dispositivo.

```
dd if=/dev/random of=/dev/da0.eli bs=1m status=progress conv=sync; sync
```

Finalizado el pequeño ataque de paranoia, debemos formatear el dispositivo.

```sh
newfs -U /dev/da0.eli
```

Y, por último, lo montamos.

```sh
mount /dev/da0.eli /media/`diskinfo -s da0`/data
```

~ DtxdF