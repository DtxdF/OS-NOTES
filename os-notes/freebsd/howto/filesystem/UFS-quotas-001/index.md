# Quotas en UFS

Las cuotas pueden ser una elegante solución cuando disponemos de una determinada cantidad de espacio de almacenamiento, y no es deseable ocuparla toda al no ser un servidor dedicado, por ejemplo. Ellas se pueden usar para limitar la cantidad de espacio o el número de archivos que puede asignar un usuario, o varios miembros de un grupo.

Para saber si tenemos configurado nuestro kernel para usar quotas, es necesario que la variable del sistema kern.features.ufs_quota esté en 1. Si no lo está, probablemente tenemos un kernel que no es GENERIC, por lo tanto, deberemos agregar lo siguiente en nuestro archivo de configuración y recompilar.

```
options QUOTA
```

Podemos dejar que FreeBSD se encargue de todo el proceso inicial, pero para indicarle ésto, deberemos agregar la opción userquota (destinado a un usuario), o groupquota (destinado a los miembros de un grupo) en nuestro fstab personal, específicamente en las opciones de la partición donde deseamos habilitar las quotas.
 Por ejemplo:

```
/dev/gpt/usr    /usr    ufs    rw,userquota,groupquota    2    2
```

Ahora deberemos habilitar las quotas en rc.conf.

```sh
sysrc quota_enable="YES"
```

Algo que podría ser discutible, son los tiempos de carga que toma quotacheck al inicio. Realmente no es recomendable deshabilitarlo para no perder la consistencia por un par de segundos extras (pero podría ser relativo). Para deshabilitarlo es tan sencillo como:

```sh
sysrc check_quotas="NO"
```

Reiniciamos.

Antes de asignar quotas a los usuarios o a los grupos, es necesario comprender algunos conceptos: algunas opciones son válidas para hacer cumplir los límites, tales como la cantidad de disco que se puede usar o la cantidad de archivos. Las asignaciones basadas en la cantidad de espacio de almacenamiento son llamadas block quotas, y la cantidad de número de archivos, inode quotas. Es posible usar los dos límites al mismo tiempo. Los límites que se imponen gracias a las quotas, se pueden partir en dos segmentos:

*.- soft limits: los límites blandos pueden ser excedidos por una cantidad limitada de tiempo, conocida como grace period, la cual es una semana por defecto. Cuando el usuario ha sobrepasado este límite y el tiempo de gracia ha llegado, este valor se convierte en un límite duro y el usuario no podrá escribir más.
*.- hard limits: los límites duros no pueden ser excedidos. Cuando el usuario intenta escribir cuando ha llegado a este límite, no tendrá éxito.

Una vez comprendidos esos conceptos, será posible ponerlos en práctica usando edquota. edquota asignará una quota para uno o más usuarios, o uno o más grupos.

```sh
edquota -hu usertest
```

Se mostrará un archivo de texto ASCII con el editor que dicte la variable de entorno EDITOR, que mayormente es vi.

```
Quotas for user usertest:
/usr: in use: 20G, limits (soft = 0, hard = 0)
        inodes in use: 2187, limits (soft = 0, hard = 0)
```

La primera línea nos da una idea de a qué usuario le asignaremos una quota. La segunda indica la partición a la cual asignaremos esa quota, con el espacio que está en uso y los límites blandos y duros de las block quotas. Se pueden asignar los límites en bytes (B), kilobytes (K), megabytes (M), gigabytes (G), terabytes (T), petabytes (P) y exabytes (E). Si no hay ninguna unidad especificada, se asumen kilobytes.

La tercera línea indica los inodes en uso de ese usuario o grupo, con sus límites blandos y duros. Se pueden asignar los límites en kiloinodes (K), megainodes (M), gigainodes (G), terainodes (T), petainodes (P) y exainodes (E). Si no hay ninguna unidad especificada, se asumen la cantidad de inodos.

En mi caso quiero que /usr para el usuario usertest tenga como límite blando 30G y como límite duro, 32G. En el caso de los inodos, como límite blando 10M y como límite duro 12M. Tengo 16M de inodes libres, no quiero que el usuario usertest malgaste todos.

```
Quotas for user usertest:
/usr: in use: 20G, limits (soft = 30G, hard = 32G)
        inodes in use: 2187, limits (soft = 10M, hard = 12M)
```

Ahora lo que se podría estar deseando es cambiar el grace period. Es tan sencillo como ejecutar edquota con el parámetro -t.

```sh
edquota -t
```

Luego se nos mostrará algo parecido a lo siguiente:

```
Time units may be: days, hours, minutes, or seconds
Grace period before enforcing soft limits for users:
/usr: block grace period: 7 days, file grace period: 7 days
```

Ahora seré un tirano y pondré 10 segundos apenas.

```
Time units may be: days, hours, minutes, or seconds
Grace period before enforcing soft limits for users:
/usr: block grace period: 10 seconds, file grace period: 10 seconds
```

Para ver las quotas:

```
quota -vh usertest

Filesystem        usage    quota   limit   grace  files   quota  limit   grace
/usr                20G      30G     32G           2187  10000000 12000000
```

\~ DtxdF