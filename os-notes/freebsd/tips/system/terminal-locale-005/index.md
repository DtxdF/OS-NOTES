# Configuración regional

Ya sea en el momento de la instalación o en los primeros pasos al usar FreeBSD notaremos un problema, que por muy sutil, puede ser una molestia, como lo es la configuración regional.

FreeBSD soporta múltiples codificaciones y lo hace muy bien, pero al inicio, independientemente del idioma que elegimos, no se configura correctamente; por suerte es algo trivial de solucionar.

Lo primero que hay que hacer es abrir el fichero /etc/login.conf y nos dirigimos a la sección default. Una vez allí tendremos que agregar lo siguiente en la última parte:

```
default:\
        :passwd_format=sha512:\
        :copyright=/etc/COPYRIGHT:\
        :welcome=/etc/motd:\
        :setenv=MAIL=/var/mail/$,BLOCKSIZE=K:\
        ...
        :umtxp=unlimited:\
        :priority=0:\
        :ignoretime@:\
        :umask=022:\
        :charset=UTF-8:\
        :lang=es_ES.UTF-8:
```

Nota: Pueden ignorar todo excepto lo que está en negrita. Se hizo de esa manera a modo de ejemplificación.

Se debe elegir la localización correcta, para ello contamos con una herramienta llamada locale que facilita la búsqueda de ellas:

```sh
locale -a
```

Esto imprimiría todos los nombres válidos siguiendo un formato como: <Idioma>_<Código del país>.<Codificación>

Posteriormente es necesario el uso de la herramienta cap_mkdb para refrescar los cambios:

```sh
cap_mkdb /etc/login.conf
```

Se pretende que se requiera configurar para todos los usuarios, por lo que tiene que tener acceso root, y en caso de querer hacerlo de manera local, simplemente se necesita modificar o crear el fichero ~/.login_conf y seguir los pasos anteriores.

Como paso final, cierren la sesión y vuelvan a iniciarla y a posteriori, ejecutar el comando locale para mostrar la configuracón actual.

## Referencias

* https://www.freebsd.org/cgi/man.cgi?query=locale
* https://www.freebsd.org/doc/handbook/using-localization.html

\~ DtxdF
