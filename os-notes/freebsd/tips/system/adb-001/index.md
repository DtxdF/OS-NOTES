# Cómo instalar adb en FreeBSD

ADB, o también conocida como Android Debug Bridge, es una herramienta de línea de comandos versátil, fácil de usar, y que permite realizar una variedad de acciones con tu Android, tanto como para proporcionar una shell UNIX. Es la navaja suiza que a veces necesitamos.

Su instalación es sumamente sencilla, y no cubre más de un comando, siendo este ejecutado con privilegios.

```sh
pkg install android-tools-adb
```

Lo siguiente es conectar nuestro teléfono. Seleccionar la opción Transferir archivos -o similar-, para luego ejecutar el siguiente comando.

```sh
adb start-server
```

Cabe aclarar, que para FreeBSD, es necesario ejecutar como root.

Este comando ejecuta un dialogo que se mostrará en nuestro teléfono para aceptar la clave RSA, siendo a partir de Android 4.2.2 necesario. El dialogo deberá decir más o menos lo siguiente:

> ¿Permitir depuración por USB?
>
> La huella digital de tu clave RSA es: XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX
>
> O Permitir siempre desde esta computadora

Donde las XXs son los números en hexadecimal de tu huella digital. La opción que aparece abajo, es clara; simplemente no se mostrará ese dialogo más.

Ya habiendo hecho todo lo anterior, es posible transferir archivos, ejecutar una shell, o cualquier cosa que sea limitado por ADB, Android y nuestras necesidades.

```sh
adb shell
poseidonlteatt:/ $
```

\~ DtxdF
