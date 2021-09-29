---
title: "Demo Instalación GNU/Linux"
date: 2021-09-29T13:09:12+06:00
author: Mark Dinn
image_webp: images/blog/meghna.webp
image: images/blog/meghna.jpg
description : "This is meta description"
---

Es una herramienta con la que podemos crear pares de llaves de autenticación para el protocolo SSH. Se utilizan para agregar una capa de seguridad a la hora de conectarnos de manera remota a otros sistemas. Consiste en crear dos largas cadenas de caracteres, una pública y una privada. La clave pública se instala en cualquier servidor y cuando intentemos conectarnos con un cliente que cuente con la llave privada, el servidor nos dará acceso. 

Esto nos permite el acceso sin la necesidad de estar utilizando contraseñas; para automatizar procesos esto va genial, sin embargo si queremos mejorar aún más la seguridad, siempre se puede aumentar la protección de la clave privada con el uso de una contraseña; como en cualquier contraseña debemos implementar una contraseña segura para que esta medida sea realmente eficaz a la hora de proteger nuestro servidor, para ello podemos utilizar servicios como [Bitwarden](https://bitwarden.com/).

## Eligiendo un algoritmo y un tamaño de clave

SSH soporta varios algoritmos de clave pública para las llaves de autenticación.

### RSA

Es la base de un conjunto de algoritmos criptográficos que se utilizan para servicios o propósitos de seguridad específicos, que permite el cifrado de clave pública y se utiliza ampliamente para proteger los datos confidenciales, principalmente cuando se envían a través de una red insegura como Internet. 

Tanto la clave pública como la privada cifran un mensaje; la clave opuesta a la utilizada para cifrar un mensaje es utilizara para descifrarlo, este atributo es una de las razones por las que RSA se ha convertido en el algoritmo asimétrico más utilizado al proporcionar un método para garantizar la confidencialidad, integridad, autenticidad. Sin embargo, en la actualidad puede ser recomendable utilizar otro algoritmo, ya que en un futuro se puede llegar a romper su seguridad. Todos los clientes SSH admiten este algoritmo.

```shell
ssh-keygen -t rsa -b 4096 
```

### DSA

Un antiguo algoritmo de firma digital del gobierno de USA. Basado en la dificultad de calcular logaritmos discretos. Normalmente se utiliza un tamaño de clave de 1021, sin embargo en su forma original ya no se recomienda.

```shell
ssh-keygen -t dsa
```

### ECDSA

Un algoritmo nuevo de firma digital estandarizado por el gobierno de USA, utilizando curvas elípticas. Posiblemente un buen algoritmo para las aplicaciones actuales. Solo admite tres tamaños de clave: 256, 384, 521 bits. La mayoría de los clientes SSH actualmente ya soportan este algoritmo.

```shell
ssh-keygen -t ecdsa -b 521 
```

### ED25519

Es un nuevo algoritmo añadido en OpenSSH. El soporte para este algoritmo en los clientes aún no es tan universal. Por lo que su uso en aplicaciones de propósito general aun no se recomienda. Sin embargo es uno de los mejores algoritmos por los que podemos optar actualmente, servicios como GitHub suelen utilizarlo para la verificación 

```shell
ssh-keygen -t ed25519
```

## Especificando directorio

La herramienta es capaz de solicitar el archivo en el que se desea almacenar la clave, pero también podemos pasarlo por la linea de comandos mediante la bandera `-f <filename>`

### Ejemplo

```shell
ssh-keygen -f ~/spawnmcgod-key-ecdsa -t ecdsa -b 521
```

## Banderas más importantes

| Bandera         | Descripción                                                  |
| --------------- | ------------------------------------------------------------ |
| -b <bits>       | Especifica el número de bits de la clave que se va a crear   |
| -C <comentario> | Proporciona un comentario de clave personalizado (se anexar al final de la clave pública |
| -p              | Solicita cambiar la frase de contraseña de un archivo de clave privada en lugar de crear una nueva clave privada |
| -t              | Especifica el algoritmo que se utilizara                     |
| -o              | Utiliza el nuevo formato OpenSSH                             |
| -N              | Proporciona una nueva frase de contraseña                    |
| -B              | Vuelca la huella digital de la clave en formato Bubble Babble |
| -l              | Vuelca la huella digital en formato SHA-2 (o MD5)            |
| -v              | Modo detallado. Muestra todos los mensajes sobre su progreso |


## Crear un par de llaves

### Paso 1  - Creación de llaves

Consiste en crear un par de llaves en el equipo cliente. En este caso se utilizará ed25519 y agregare un comentario para identificarlo de manera mas fácil 

```shell
ssh-keygen -t ed25519 -C "spawnmc@spawnmc.me"
```

### Paso 2 - Especificando lugar para guardar las llaves

Después de lanzar el comando anterior, se nos mostrará un mensaje donde nos pide que ingresemos la ruta. Este paso es opcional, si deseamos utilizar la ubicación predeterminaba bastará con pulsar <ENTER>. 

Otra cosa que podríamos cambiar es el nombre, para ello basta con utilizar un punto seguido del nombre que le queramos dar. Ejemplo: `.spawnmc-key`.

```shell
~
➜ ssh-keygen -t ed25519 -C "spawnmc@spawnmc.me"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/spawn/.ssh/id_ed25519):
```

### Paso 3 - Asignar una contraseña

Después de indicar el lugar en donde queremos guardar nuestras llaves, se nos mostrará un nuevo mensaje, donde nos pedirá que ingresemos una contraseña, sin embargo también es opcional, aunque lo más recomendable es siempre mantener el mayor de seguridad posible ya que si un usuario no autorizado cuenta con la llave privada podrá ingresar a cualquier servidor en el que se haya configurado la llave pública asociada.

```shell
~
➜ ssh-keygen -t ed25519 -C "spawnmc@spawnmc.me"
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/spawn/.ssh/id_ed25519): .spawnmc-key
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in .spawnmc-key
Your public key has been saved in .spawnmc-key.pub
The key fingerprint is:
SHA256:5GGozfTjOPR4zVmFj29AGlpm8rI0HNH5Krsp5W0ZVOc spawnmc@spawnmc.me
The key's randomart image is:
+--[ED25519 256]--+
|        .. .     |
|       . .o ...  |
|      o * =ooo.  |
|     = * X.+.+E  |
|    . + S.o.+ .  |
|     . *oBoo o   |
|      +o=++o  o  |
|      .oo.+  .   |
|       .oo       |
+----[SHA256]-----+

~ took 9s
➜
```


### Paso 4 - Copiar la llave pública en el servidor

Para que podamos hacer uso de las llaves es necesario tener la llave pública en nuestro servidor, para ello se utiliza `ssh-copy-id` siguiendo la sintaxis `ssh-copy-id -i <ruta_de_la_llave_publica> user@host`.

La ruta predeterminada se encuentra en `~/.ssh`

Una vez que ingresemos el comando se nos pedirá nuestra contraseña del usuario asignado a nuestro servidor, bastará con ingresarla y se copiará nuestra llave pública.

Si nuestro servidor está configurado en un puerto diferente al predeterminado debemos indicarlo con la bandera `-p <puerto>`

  Ejemplo:

  `ssh-copy-id -i .spawnmc-key.pub spawn@192.168.100.45 -p 3777`


```shell
~
➜ ssh-copy-id -i .spawnmc-key.pub spawn@192.168.100.45
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".spawnmc-key.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
spawn@192.168.100.45's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'spawn@192.168.100.45'"
and check to make sure that only the key(s) you wanted were added.


~ took 5s
➜
```

### Paso 5 - Probando el correcto funcionamiento

Para probar que todo funciona correctamente intentaremos conectarnos a nuestro servidor remotamente siguiendo la sintaxis `ssh -i <ruta_de_la_llave_publica> user@host`

En caso de estar usando la ruta predeterminada de las llaves no es necesario incluir la bandera `-i`

Si nuestro servidor está configurado en un puerto diferente al predeterminado debemos indicarlo con la bandera `-p <puerto>`

  Ejemplo:

  `ssh -i .spawnmc-key spawn@192.168.100.45 -p 3777`

```shell
~
➜ ip a show wlan0 | grep -E -o "([0-9]{1,3}[\.]){3}[0-9]{1,3}\/"
192.168.100.11/

~
➜ ssh spawn@192.168.100.45 -i .spawnmc-key
Last login: Fri Aug 20 15:12:10 2021 from 192.168.100.11
[spawn@arch ~]$
[spawn@arch ~]$
[spawn@arch ~]$ ip a show wlan0 | grep -E -o "([0-9]{1,3}[\.]){3}[0-9]{1,3}\/"
192.168.100.45/
[spawn@arch ~]$
```

Y listo, estaremos dentro de nuestro servidor. ahora podremos realizar nuestras actividades de una manera más segura.

**La seguridad de SSH puede seguir aumentando cambiando variables dentro del servidor y más medidas de hardening, sin embargo eso sale del alcance de este pequeño articulo.**

## Referencias

> https://searchsecurity.techtarget.com/definition/RSA
https://www.openssh.com/txt/release-8.2
http://man.openbsd.org/OpenBSD-current/man1/ssh-keygen.1#NAME
https://www.ssh.com/academy/ssh/keygen
https://www.ssh.com/academy/ssh/copy-id
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2
https://www.digitalocean.com/community/tutorials/como-configurar-las-llaves-ssh-en-ubuntu-18-04-es
https://www.stackscale.com/es/blog/configurar-llaves-ssh-servidor-linux/
https://www.linuxtotal.com.mx/index.php?cont=info_seyre_010
https://datatracker.ietf.org/doc/html/rfc4254
