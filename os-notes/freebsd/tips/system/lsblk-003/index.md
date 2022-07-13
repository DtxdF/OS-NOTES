# lsblk en FreeBSD

En FreeBSD contamos con múltiples herramientas como gpart, usbconfig o camcontrol para mostrar los dispositivos y entre otras cosas, pero hay veces que nos gustaría a los usuarios de BSD tener una salida muy parecida al comando lsblk de Gnu/Linux.

Para poder usar una herramienta parecida a lsblk simplemente seguimos los siguientes pasos:

```
fetch https://raw.githubusercontent.com/vermaden/scripts/master/lsblk.sh
chmod u+x lsblk.sh
mv lsblk.sh lsblk
./lsblk
```

## Referencias

* https://www.reddit.com/r/freebsd/comments/d9rup4/list_block_devices_on_freebsd_lsblk8_style/

\~ DtxdF
