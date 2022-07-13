# Cómo usar OPIE en FreeBSD

OPIE, de las siglas One-time Passwords In Everything, es una solución frente a algunos ataques, tales como los sniffers. La idea, como su mismo nombre lo indica, es usar una contraseña una sola vez, por lo tanto, aunque un tercero pudiera obtener la contraseña que viaja a través de nuestros paquetes en una conexión no encriptada, éste se vería frustrado al tratar de acceder al sistema.

En la actualidad existen alternativas a FTP, pero todavía éste sigue siendo útil en algunos contextos, como sistemas heredados o proveer archivos a la mayor cantidad de personas posibles. Quizá tengamos que hacer tareas administrativas, o subir un archivo, pero puede que en la red a la cual estemos conectados no es segura. Ahí es donde entra OPIE a salvarnos.

Primero que nada, es necesario ejecutar opiepasswd(1):

```sh
opiepasswd -fc
```

El parámetro -c ajusta el modo consola donde se espera que el usuario tenga acceso seguro al sistema. -f fuerza a opiepasswd(1) a continuar incluso cuando no debería. Estoy en una sesión SSH, estoy seguro por lo tanto.

La salida del comando anteriormente ejecutado debería ser más o menos la siguiente.

```
Adding usertest:
Only use this method from the console; NEVER from remote. If you are using
telnet, xterm, or a dial-in, type ^C now or exit with no password.
Then run opiepasswd without the -c parameter.
Using MD5 to compute responses.
Enter new secret pass phrase: 
Again new secret pass phrase: 

ID usertest OTP key is 499 dt2067
BASK TIME HOT BIT ACRE RICE
```

ID es nuestro identificador de usuario. El conteo de iteración por defecto es 499 (500, pero se generó una contraseña, así que disminuyó a 499). La semilla es dt2067. Y por último, la contraseña es BASK TIME HOT BIT ACRE RICE.

Podemos usar opiekey(1) para seguir generando contraseñas de un solo uso.

```sh
opiekey 499 dt2067
```

opiekey(1) recibe dos argumentos. El primero es el conteo. El segundo, la semilla. Es importante tener en cuenta esto, aunque no es necesario recordarlo en la mayoría de casos, los programas que hacen uso de OPIE nos los recordarán.

Quizá tengamos que hacer un par de veces en periodos de tiempo indeterminados. opiekey(1) tiene la posibilidad de ajustarse a esta necesidad.

```sh
opiekey -n 10 498 dt4152
```

Esto generará 10 contraseñas de un solo uso. Se deberán guardar en papel y tener cerca un encendedor. Solo para tomar precauciones...

Siguiendo el ejemplo de FTP, accedamos, pero, podremos solo usar las contraseñas de un solo uso.

```sh
ftp dtxdf-laptop.lan
Connected to dtxdf-laptop.lan.
220 dtxdf-laptop.lan FTP server (Version 6.00LS) ready.
Name (dtxdf-laptop.lan:dtxdf): dtxdf
331 Response to otp-md5 493 dt097
0 ext required for dtxdf.
Password:
```

En este punto debemos usar opiekey(1) como se dijo en las anteriores secciones.

```
opiekey 493 dt0970
Using the MD5 algorithm to compute response.
Reminder: Don't use opiekey from telnet or dial-in sessions.
Enter secret pass phrase: 
THEY AVER MUFF LAM EDEN TRAM
```

Copiamos o escribimos la contraseña para usarla en el inicio de sesión de FTP.

```
230 User dtxdf logged in, access restrictions apply.
Remote system type is UNIX.
Using binary mode to transfer files.
```

Así de sencillo es asegurar nuestras contraseñas de terceros, pero no nos protege de las modificaciones indeseadas, y es más recomendable usar alternativas a FTP-plano como SFTP o FTPS.

## Referencias:

* opiepasswd(1)
* opiekey(1)
* opieinfo(1)

\~ DtxdF