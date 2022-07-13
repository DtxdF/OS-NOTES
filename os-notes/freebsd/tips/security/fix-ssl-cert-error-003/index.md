# Cómo arreglar error de SSL/TLS de fetch(1)

Al usar las primeras veces utilidades que usen SSL, como fetch o el mismísimo wget o incluso curl, nos podrá salir un mensaje de error verificando el certificado SSL de nuestro sistema.

En caso de que eso suceda, simplemente es necesario instalar el paquete ca_root_nss.

```sh
pkg install ca_root_nss
```

Una vez que finalice la instalacón, se debe ingresar a una página web que se requiera SSL, como ifconfig.me

```sh
curl -i https://ifconfig.me
```

\~ DtxdF