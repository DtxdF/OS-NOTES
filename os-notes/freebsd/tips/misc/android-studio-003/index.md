# Instalación de Android Studio en FreeBSD

## Preparación

Es necesario tener iniciar linuxlator [1]. SI se desea iniciar al inicio del sistema, es necesario agregar la siguiente línea a `/etc/rc.conf`:

```sh
linux_enable="YES"
```

Ahora solo se debe iniciar el servicio:

```sh
# service linux start
```

Instalamos openjdk11 y android-tools:

```sh
pkg install openjdk11 android-tools
```

Una vez finalizada la instalación de openjdk11, tendremos que montar `fdescfs(5)` y `procfs(5)`, si todavía no lo hemos hecho.

```sh
mount -t fdescfs fdesc /dev/fd
mount -t procfs proc /proc
```

Ahora para que los cambios sean permanentes, es necesario agregarlos a `/etc/fstab`.

```
fdesc   /dev/fd         fdescfs         rw      0       0
proc    /proc           procfs          rw      0       0
```

## Android Studio

Hay que [descargar la versión para Linux de Android Studio](https://developer.android.com/studio#downloads).

Una vez descargado, lo extraemos e ingresamos al directorio:

```sh
tar xvf android-*.tar.gz
cd ./android-studio
```

Para que sea efectiva la ejecución de Android Studio es necesario definir la variable `JAVA_HOME` en el entorno del script `bin/studio.sh`:

```sh
env JAVA_HOME=/usr/local/openjdk11/ bin/studio.sh
```

Si no se desea definir la variable cada vez que ejecutamos Android Studio, podríamos añadirla a la variable `PATH`.

Instalamos los SDK necesarios y aceptamos las licencias. Posteriormente, creamos una aplicación con lenguaje base, Java.

En la barra de herramientras, ejecutamos `FIle > Settings > Build, Execution, Deployment > Gradle`.

En `Gradle Projects` seleccionamos `Gradle JDK` y seleccionamos `Android Studio java home /usr/local/openjdk11`.

<p align="center">
    <img src="https://imgur.com/Ih6Hj04.png" />
</p>

Cerramos Android Studio y al iniciar nuevamente Gradle nos dirá que no reconoce la plataforma FreeBSD.

Nos dirigamos a `build.gradle (Project)`

**Nota**: No confundir con módulo.

En `build.gradle`, en la parte superior, nos indica lo siguiente:

```
//Top-level build file where you can add configuration options common to all
sub-projects/modules.
```

Debemos añadir lo siguiente:

```
buildscript {
    System.setProperty("os.name","Linux")
}
```

<p align="center">
    <img src="https://imgur.com/QihgkY8.png" />
</p>

Guardamos el fichero y reiniciamos Android Studio.

## React Native

Los últimos requerimientos faltantes son muy simples y se listan a continuación:

```sh
# Se instala pip primero:
pkg install py39-pip

# Luego con pip se instala npm:
pip install npm

# Ahora se instala React-Native:
npm -g install react-native

# Y por último npx:
npm -g install npx
```

Es necesario configurar nuestro entorno de desarrollo, por lo que sería excelente leer la guía oficial [Configuración del entorno de desarrollo](https://reactnative.dev/docs/environment-setup).

Después de haber creado nuestro proyecto editamos el `build.gradle` ubicado en `android/`.

<p align="center">
    <img src="https://imgur.com/w4RUV9C.png" />
</p>

Y editamos buildscript añadiendo la misma línea que en Android al Principio del mismo:

```sh
System.setProperty("os.name","Linux")
```

<p align="center">
    <img src="https://imgur.com/L7v9FgW.png" />
</p>

Ya podremos compilar nuestra APK mediante:

```sh
npx react-native run-android
```

**Importante**: Al intentar comunicarse nuestro dispositivo, el adb que nos brinda android-sdk dará error, por lo que debemos remplazarlo con el adb instalado mediante android-tools.

```sh
ln -s /usr/local/bin/adb ~/Android/Sdk/platform-tools/adb
```

Matamos adb:

```sh
adb kill-server
```

Lo volvemos a ejecutar:

```sh
adb start-server
```

Comprobamos que podemos ver nuestro dispositivo:

```sh
adb devices
```

Si aparece, ya podemos trabajar.

## Notas

* Inspiración: https://zewaren.net/android-1.html
* No hace falta tener que compilar SdkConstants.java puesto que se pueden establecer los valores de la VM mediante buildscript y si se ve como trabaja el código fuente de Aapt2 simplemente, lee la propiedad: os.name que le pasa la máquina virtual llamada por gradle. Sin afectar a la principal de Android Studio o React Native porque emplean su propia Java VM.

## Agradecimientos

Agradecimientos a Jart/JartX por la nota.

## Referencias

1. https://docs.freebsd.org/en/books/handbook/linuxemu