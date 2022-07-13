# Cómo configurar el mapa del teclado

Ya sea la primera o mucho más de la segunda vez cuando le damos uso a xorg, no nos viene configurado correctamente el diseño del teclado, o en otras palabras la ubicación estandarizada de las teclas.

Para ello contamos con una herramienta muy útil para determinar el diseño del teclado, llamada setxkbmap.

Su uso es simple:

```sh
setxkbmap <Diseño>
```

Por ejemplo, para ajustar a un diseño español, se hace lo siguiente:

```sh
setxkbmap es
```

Sin embargo, su uso es recomendable para probar cómo se vería un diseño mientras dure la sesión; si queremos que sea persistente, se debe agregar un archivo de configuración en /usr/local/etc/X11/xorg.conf.d.

Por ejemplo, podríamos (por convención) llamar al archivo de configuración keyboard-es.conf indicando que es una configuración del teclado con un diseño español, aunque el nombre es irrelevante para xorg, su significado es más útil para nosotros. El contenido podría ser algo como:

**/usr/local/etc/X11/xorg.conf.d/keyboard-es.conf**:

```
Section  "InputClass"
  Identifier  "KeyboardDefaults"
  MatchIsKeyboard  "on"
  Option    "XkbLayout" "es"
EndSection
```

Nota: Esta configuración y la herramienta setxkbmap también podría ser perfectamente portada a otro sistema unix/unix-like.

## Referencias:

* https://www.freebsd.org/doc/handbook/x-config.html
* https://www.freebsd.org/cgi/man.cgi?query=setxkbmap

\~ DtxdF