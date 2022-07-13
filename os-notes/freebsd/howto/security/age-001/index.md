# Age, la alternativa a GnuPG

Age, pronunciado aghe, es una herramienta, formato y librería simple, moderna y segura de cifrado de archivos escrita en Golang. Age sigue el clásico estilo unix. Es compatible con las claves SSH. Y las claves que genera la herramienta por defecto son ed25519, por lo tanto, son realmente pequeñas.

Age usa cifrado asimétrico (por defecto), por lo tanto es necesario contar con una clave pública y otra secreta. Con age solo es necesario almacenar la clave secreta y es posible calcular las veces que deseemos la clave pública a partir de la anterior.

```sh
mkdir ~/.age
age-keygen -o ~/.age/age_ed25519
Public key: age16tpyaw6ye069rhl286depe8sga848ul758su4k8vc8v02zvhkvuqtsqnf4
```

Se la debemos enviar a las personas que deseen comunicarse con nosotros o ponerlas en nuestro blog, o sitio público, recordando además que no se debe compartir la clave secreta.

Si deseamos almacenar la clave pública en nuestro disco, es tan sencillo como calcularla a partir de la clave secreta, como se mencionó anteriormente.

```sh
age -y ~/.age/age_ed25519 > ~/.age/age_ed25519.pub
```

Supongamos que queremos hacer un respaldo completo de un directorio y, además, cifrarlo con nuestra propia clave pública. Es tan sencillo con combinar bsdtar(1) y age.

```sh
tar -C /home -cpvJf - . | age -R ~/.age/age_ed25519.pub -o home.txz.age
```

Para restaurarlo:

```sh
age -d -i ~/.age/age_ed25519 home.txz.age | tar -C /home -xvf -
```

Incluso podemos cifrar cualquier cosa que sea un archivo (en el mundo de unix, todo).

```sh
# Bob
age-keygen -o bob

# Alice
age-keygen -o alice

# Bob
echo "¡Hola, Alice!" | age -r age15u8a6lme4j6tcx8zqynk7trle0g9lh67se79ace67f96z9x5y3ssxvtzsu > alice.msg.age

# Alice
age -i alice -d alice.msg.age

¡Hola, Alice!
```

El cifrado simétrico también es posible con Age.

```sh
age --passphrase --armor -o myself.msg.age

Enter passphrase (leave empty to autogenerate a secure one): 
Confirm passphrase:
# Para descifrarlo:
age -d myself.msg.age
Enter passphrase:
Hello, world!
```

Sitio web oficial: https://github.com/FiloSottile/age

Age escrita en Rust (rage): https://github.com/str4d/rage

\~ DtxdF
