**doas** es un programa para ejecutar comandos como otro usuario. El administrador del sistema puede configurarlo para otorgar privilegios a usuarios específicos para ejecutar comandos específicos. Es gratuito y de código abierto bajo la licencia ISC y está disponible en Unix y varios sistemas Unix-like.

<p align="center">
	<img src="https://i.imgur.com/jInF5Tx.png" />
</p>

**doas** es lo más simple que puedes conseguir teniendo muchas características del veterano **sudo**, no obstante a pesar de que éste último sea prácticamente un estándar de facto en la mayoría de sistemas, hay otros como OpenBSD que ya lo traen por defecto. Y no es por nada, doas es realmente fácil de usar y de configurar, ciertamente éso es debido a los patrones que contiene que analizaremos en brevedad.

# Instalación

Para la instalación en Gnu/Linux se usará **debian**, pero son libres de usar cualquier distribución de preferencia y que sea compatible. En el caso de FreeBSD, simplemente se instalará desde los repositorios.

## Debian

```bash
sudo apt install build-essential make bison flex libpam0g-dev 
git clone https://github.com/slicer69/doas.git
cd doas
sudo make install
```

## FreeBSD

```bash
su
pkg install doas
```

**Nota**: Al concluir la instalación de **doas** en FreeBSD tendremos un archivo de configuración de ejemplo en '**/usr/local/etc/doas.conf.sample**'. Si se desea usar ese y modificarlo según nuestros datos, mucho mejor, como lo haremos en este caso.

# Configuración

Como se comentó anteriormente, **doas** provee patrones muy fáciles de aprender y comprender. El patrón es: **permit|deny [options] identity [as target] [cmd command [args ...]]**

**permit|deny**: permit, permite la opción o el comando de **doas** escrito en la configuración; deny, es la contraparte de permit, por lo que deniega la opción o el comando escrito en la configuración.
**[options]**: Las opciones del comando u opción a tratar. Ya se describirá mejor este apartado en unos instantes.
**identity**: El nombre de usuario o grupo que está ejecutando el comando. En el caso de un usuario, se coloca una cadena de caracteres (como: **dtxdf**), pero en el caso de un grupo se colocan dos puntos y una cadena de caracteres como se mencionó (como: **:wheel**).
**[as target]**: Ejecutar una opción o comando como un usuario o grupo en específico.
**[cmd command [args ...]]**: Ejecutar el comando siguiendo las opciones especificadas anteriormente.

**Nota**: Todo lo que esté en **|** significan que se pueden ejecutar una de esas opciones (como en: permit|deny). Todo lo que esté entre corchetes, es opcional (como en: [options]).

[options] tienes una variedad de opciones que permiten cambiar el comportamiento de cada comando. Entre ellas están:

* **nopass**: No se requiere que el usuario ingrese una contraseña
* **persist**: Después de que el usuario se autentica con éxito, no vuelva a solicitar una contraseña durante algún tiempo. Funciona en OpenBSD solamente, La opción **persist** no está disponible en Linux o FreeBSD.
* **keepenv**: Mantener el entorno de usuario por defecto. Para poder ejecutar la mayoría de aplicaciones GUI, el usuario debe tener la palabra clave **keepenv** especificada, ya que en caso de que no sea así, puede bloquear la aplicación de forma indefinida debido a una mala información que se le proporcione.
* **setenv {[variable ...] [variable=value ...]}**: Se pueden especificar variables de entorno arbitrarias o incluso se pueden eliminar las ya definidas usando un guion (**-**) al inicio del nombre de ella.

Ahora siguiendo el archivo de configuración de ejemplo que está en **/usr/local/etc/doas.conf.sample**, vamos a copiarlo a **/usr/local/etc/doas.conf** y empecemos a configurar:

```text
# Archivo de ejemplo para doas
# Por favor consulte la página de manual de doas.conf(5) para obtener información sobre
# cómo configurar un archivo doas.conf.

# Permitir a los miembros del grupo wheel ejecutar acciones como root
permit :wheel

# Permitir al usuario alice ejecutar comandos como el usuario root.
permit alice as root

# Permita que el usuario bob ejecute programas como root, manteniendo las variables de entorno. Útil para aplicaciones GUI.
permit keepenv bob as root

# Permita que el usuario cindy ejecute solo el administrador de paquetes pkg como root para realizar actualizaciones de paquetes.
permit cindy as root cmd pkg update
permit cindy as root cmd pkg upgrade
```

Otro ejemplo que podría resultar interesante en caso de no deseas ejecutar **poweroff** o **reboot** sin requerir contraseña es:

```text
permit nopass user as root cmd poweroff
permit nopass user as root cmd reboot
```

O también podríamos denegar el acceso a una operación:

```text
deny user cmd poweroff
```

Esto resultaría:

```text
doas poweroff
doas: Operation not permitted
```

**Nota**: **user** debe ser reemplazado por tu nombre de usuario.

# ¿Cuál es mejor?

Ninguno. **sudo** por un lado es complejo, y tiene muchas opciones de configuración que le ofrece a un administrador de sistemas versatibilidad, pero **doas** es perfecto para la mayoría de usuarios que solo requieren ejecutar una tarea con privilegios.

# Material de referencia y recomendado

* www.freebsd.org/cgi/man.cgi?query=doas.conf
* https://en.wikipedia.org/wiki/Doas

\~ DtxdF
