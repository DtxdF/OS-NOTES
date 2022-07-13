# Configurar el mapa de de caracteres para la terminal

Configurar el mapa de caracteres no es lo único que se debe tener en cuenta para poder usar FreeBSD sin causar efectos indeseados.

La distribución del teclado indica en qué ubicación o cómo se deben asociar las teclas que presionamos, es por ello que es muy importante.

Contamos con una utilidad llamada kbdmap que es una herramienta con una interfaz TUI (Text User Interface) o basada en texto, muy interactiva que permite cambiar la distribución del teclado.

Al ejecutarla se nos mostrará una menú y las teclas a usar serán las siguientes:

* Arriba y abajo: Mueve el cursor entre los idiomas a seleccionar.
* Enter: Selecciona un idioma.
* Derecha e izquierda: Mueve el cursor entre «OK» y «Cancel»

Una vez elegido el idioma, en nuestro caso "Español (con acentos)" (el primero), presionamos enter con lo que saldremos y se nos imprimirá un pequeño texto como el siguiente:

```sh
keymap="es.kbd"
```

Notaremos que ahora podremos usar correctamente símbolos como el euro o el dolar pero hay que tener en cuenta que es mientras dure la ejecución, por lo que para hacer que los cambios persistan necesitamos abrir el archivo /etc/rc.conf y literalmente cambiar la clave (en caso de que la tengamos ajustada) o agregar el texto impreso con kdbmap.

Por ejemplo:

```sh
/etc/rc.conf:
clear_tmp_enable="..."
syslogd_flags="..."
sendmail_enable="..."
hostname="..."
keymap="es.kbd"
ifconfig_re0="..."
```

Nota: Ignoren las demás claves, es a modo de ejemplificación.

Una vez guardado el fichero, reiniciamos, iniciamos sesión y presonamos combinaciones de teclas para verificar que todo haya salido bien.

* https://www.freebsd.org/cgi/man.cgi?query=kbdmap

\~ DtxdF
