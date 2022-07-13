# Cómo seleccionar el mirror correcto

Al seleccionar un «mirror» en la instalación de FreeBSD es importante tomar en cuenta la ida y vuelta de paquetes y si éste está sincronizado correctamente para tener una instalación libre de problemas.

La siguiente página es de mucha utilidad y hay que tenerla en cuenta: https://people.freebsd.org/~pav/mirrors.dead/

Pero antes de haber elegido el correcto, es necesario saber el tiempo de ida y vuelta de los paquetes, como se mencionó anteriormente.

Para ello FreeBSD (y otros sistemas unix-like) cuentan con una utilidad llamada ping.

Un ejemplo realista sería a un «mirror» que está ubicado en Brasil:

```sh
ping -c 4 ftp3.br.FreeBSD.org
```

Lo cual se podría obtener (marcando lo relevante):

```
PING ftp3.br.FreeBSD.org (143.106.10.152) 56(84) bytes of data.
64 bytes from venus.unicamp.br (143.106.10.152): icmp_seq=1 ttl=50 time=190 ms
64 bytes from venus.unicamp.br (143.106.10.152): icmp_seq=2 ttl=50 time=201 ms
64 bytes from venus.unicamp.br (143.106.10.152): icmp_seq=3 ttl=50 time=191 ms
64 bytes from venus.unicamp.br (143.106.10.152): icmp_seq=4 ttl=50 time=190 ms

--- ftp3.br.FreeBSD.org ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 5ms
rtt min/avg/max/mdev = 189.994/193.066/200.866/4.520 ms
```

Lectura recomendada: https://www.freebsd.org/cgi/man.cgi?query=ping

\~ DtxdF