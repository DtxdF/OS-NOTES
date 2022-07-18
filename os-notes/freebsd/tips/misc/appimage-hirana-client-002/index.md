Como bien es sabido, Hirana es un proyecto independiente de underc0de, pero que están muy relacionados, tanto así que al acceder a la página del foro, podemos ingresar directamente para poder conversar, ya sea con el *staff* de underc0de, o con quien se desee.

<p align="center">
    <img src="https://imgur.com/HprOeIJ.png" />
</p>

Hirana-Client está, desde el momento de escribir este artículo, para varias plataformas como Windows, Gnu/Linux, MacOS e incluso no hay necesidad de instalarlo dado que es totalmente posible usarlo desde **irc.underc0de.org**, **web.hirana.net**, o como ya se aclaró, desde el mismo foro. [Alex](https://github.com/alexander171294), el creador de este proyecto, menciona que próximamente Hirana-Client tendrá su sitio en las plataformas como IOS y Android.

<p align="center">
    <img src="https://imgur.com/3bSYK3S.png" />
</p>

Aunque no esté oficialmente documentado, ayer (2022-03-01), quise instalar este cliente para mi caja personal con FreeBSD. Usando la tecnología Linuxlator, es posible ejecutar una aplicación AppImage con un poco de esfuerzo.

# Instalación de Hirana-Client

Primero, antes de iniciar, es necesario descargar el cliente desde el [repositorio oficial](https://github.com/hirananet/HiraClient/releases).

<p align="center">
    <img src="https://imgur.com/ANgaAoC.png" />
</p>

Se puede apreciar varios formatos, que en nuestro caso será el que está destinado para Linux, o en otras palabras, [Hirana-Client-2.1.1.AppImage](https://github.com/hirananet/HiraClient/releases/download/v2.1.1/Hirana-Client-2.1.1.AppImage)

**Nota**: en este artículo se usará la versión 2.1.1 del cliente, pero hay que ser responsables en usar la última versión estable que pudiera cambiar en el futuro.

## Instalación

Una vez descargado, y ya habiéndole dado los permisos adecuados de ejecución, pudieramos antes de ejecutarlo, ser organizados.


```bash
mkdir -p ~/.local/opt/Hirana-Client
mv Hirana-Client-2.1.1.AppImage ~/.local/opt/Hirana-Client
cd ~/.local/opt/Hirana-Client
./Hirana-Client-2.1.1.AppImage --no-sandbox
```

<p align="center">
    <img src="https://imgur.com/T75TuS5.png />
</p>

*Esto no será tan fácil como se esperaba...*

El error es claro, y se necesita *fuse* para que pueda funcionar sin problemas, pero el problema es que aunque se instale el paquete *libfuse2* (en ubuntu usando la capa de compatibilidad con linux; no se está refiriendo a instalarlo en FreeBSD), el sistema seguirá sin reconocerlo luego de, incluso, instalarlo.

<p align="center">
    <img src="https://imgur.com/JcMII3p.png" />
</p>

Por suerte, el mismo AppImage nos da una pista de lo que hay que hacer.

```bash
./Hirana-Client-2.1.1.AppImage --appimage-extract
```
<p align="center">
    <img src="https://imgur.com/Mbpjj8P.png" />
</p>

Esto nos creará un interesante directorio llamado *squashfs-root*, donde tendremos lo necesario para poder ejecutar la aplicación.

```bash
./squashfs-root/hira-client-desktop --no-sandbox
```

<p align="center">
    <img src="https://imgur.com/9X2b35v.png" width="800" height="451" />
</p>

## Mejoras

He mentido al decir que será difícil, porque no lo ha sido, pero aunque podamos ejecutarlo sin problemas, tenemos un gran problema que probablemente no se haya apreciado.

<p align="center">
    <img src="https://imgur.com/CubnNXq.png" />
</p>

*¡210 Megabytes!*

Por suerte, hay una solución sencilla.

Primero, hay reducir el espacio, lo cual se puede hacer con *tar(1)*. Usaré la compresión *zstd* porque es muy *cool*, y aunque pudiera usar perfectamente *xz*, amo el equilibrio entre ratio de compresión y tiempo estimado, donde claramente *zstd* gana.

```bash
cd ./squashfs-root
tar --zstd --options zstd:compression-level=3 -cvf hirana-client.tzst .
```

<p align="center">
    <img src="https://imgur.com/Pqj0Kq2.png" width="800" height="386" />
</p>

El nivel de compresión dependerá de qué tan rápido sea su procesador. Mi pentium hace todo lo posible para comprimir lo más rápido que pueda, pero no es posible hacer magia (ya lo he intentado).

<p align="center">
    <img src="https://imgur.com/6Y6ULkd.png" />
</p>

Eso sería poco menos de 2.3 veces menos su peso original.

Solucionando el primer problema, viene el segundo: *¿cómo puedo ejecutar un .tzst?* No puedes, o al menos no directamente. Empieza la magia. Mi idea es aprovechar lo que nos brinda FreeBSD: vamos a crear un sistema de archivos que va a estar en memoria, descomprimimos *hirana-client.tzst* y luego lo ejecutamos. Suena complicado, pero realmente FreeBSD nos pone las herramientas para hacerlo fácilmente.


```bash
# Creamos la carpeta donde se montarán los archivos
mkdir mnt
# Creamos un disco en memoria
doas mdconfig -a -t malloc -o reserve -s 238M
# Formateamos con UFS sin soft-updates ni journaling, ni con el directorio .snap. No es necesario nada de esto.
doas newfs -n /dev/md0
# Montamos el sistema de archivos.
doas mount /dev/md0 mnt
# Descomprimidos.
doas tar -C mnt/ -xvf hirana-client.tzst
```
<p align="center">
    <img src="https://imgur.com/lRO6EuV.png" />
</p>

Notaremos que fue muy rápido, pero el siguiente paso es simplemente ejecutar *hira-client-desktop* en el directorio *./mnt/*.

### Atajos

Necesitamos ejecutarlo desde nuestro menú de nuestro entorno, por suerte, para las aplicaciones que sigan el estándar Freedesktop, podemos colocar el .desktop en ~/.local/share/applications.

```bash
cp ~/.local/opt/Hirana-Client/mnt/hira-client-desktop.desktop ~/.local/share/applications
# Copiamos el icono
cp ~/.local/opt/Hirana-Client/mnt/usr/share/icons/hicolor/0x0/apps/hira-client-desktop.png  ~/.local/share/icons/hicolor/256x256/apps
sed -i '' 's/AppRun/.local\/opt\/Hirana-Client\/hirana-client.sh --no-sandbox/' ~/.local/share/applications/hira-client-desktop.desktop
```

Hay una línea de más muy rara.

Si seguimos el tutorial anterior, vemos la manera de usar el Hirana-Client descomprimiéndolo en memoria, pero hacer ese proceso (reservar memoria, formatear, montar, etc.) puede tomar mucho tiempo, por lo que lo mejor que hay que realizar es automatizar.

**~/.local/opt/Hirana-Client/hirana-client.sh**:

```bash
#!/bin/sh

# Compressed file path
APPIMAGE_FILE=~/.local/opt/Hirana-Client/hirana-client.tzst
# Memory reservation
APPIMAGE_SIZE=238M
# root path
PREFIX=~/.local/opt/Hirana-Client/mnt
# Reserved
MD_DEVICE=
# Relative
APPIMAGE_RUN="hira-client-desktop $@"

release() {
        if [ -z "${MD_DEVICE}" ]; then
                # nothing to do
                return 1
        fi

        doas umount "${PREFIX}"
        doas mdconfig -du ${MD_DEVICE}
}

main() {
        MD_DEVICE=`doas mdconfig -a -t malloc -o reserve -s ${APPIMAGE_SIZE}`
        doas newfs -n /dev/${MD_DEVICE}
        doas mount /dev/${MD_DEVICE} "${PREFIX}"
        doas tar -C "${PREFIX}" -xf "${APPIMAGE_FILE}"
        sh -c "${PREFIX}/${APPIMAGE_RUN}"
}

set -e

trap release SIGTERM SIGINT EXIT

main
```

Este script hace todo lo que realizamos en el paso anterior e incluso lo ejecuta. Usa *doas* (pueden ver un tutorial de doas en FreeBSD [aquí](https://underc0de.org/foro/gnulinux/doas-una-alternativa-a-sudo-simple-ligera-y-segura/)). Dependiendo de sus necesiades, pueden configurarlo para que se ejecute sin contraseña con determinados comandos, o pueden dejarlo a su creatividad y mejor solución.

<p align="center">
    <img src="https://imgur.com/aR7W8kj.png" width="800" height="449">
</p>

Ya teniendo todo lo necesario, ¡el hirana-client está instalado! Ya es posible conversar con las personas del foro.

<p align="center">
    <img src="https://imgur.com/5lteZRD.png" width="800" height="449">
</p>

# Conclusión

Este tutorial está destinado a cómo poder usar Hirana-Client en FreeBSD, pero tiene como segundo objetivo usar una AppImage, que aunque el proceso puedo ser distinto para cada aplicación, por lo menos orienta un poco a los usuarios de esta hermosa plataforma.

\~ DtxdF
