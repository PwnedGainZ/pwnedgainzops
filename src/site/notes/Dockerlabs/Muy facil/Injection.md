---
{"-publish":true,"permalink":"/dockerlabs/muy-facil/injection/","PassFrontmatter":true}
---

![[Pasted image 20250525004817.png\|Pasted image 20250525004817.png]]


Empezamos haciendo un nmap para ver los puertos abiertos de la ip:

```
nmap -sCV -T5 172.17.0.2 -v -oN injection
```

![Pasted image 20250525005259.png](/img/user/imgs/Pasted%20image%2020250525005259.png)

Obsevamos que tenemos el puerto 22 y 80 abierto, vamos a colocar la ip dentro de firefox:

![Pasted image 20250525005437.png](/img/user/imgs/Pasted%20image%2020250525005437.png)

Tenemos un login y como dice su mismo nombre dice injection podemos intuir alguna injeccion sql, por lo cual probamos un payload basico.

![Pasted image 20250525005521.png](/img/user/imgs/Pasted%20image%2020250525005521.png)

Listo nos vota una password y un usuario, por lo tanto tambien sabemos que tenemos el puerto ssh(22) abierto entonces intentamos con las credenciales.

```
dylan:KJSDFG789FGSDF78
```

Y listo tenemos acceso a ssh:
![Pasted image 20250525005723.png](/img/user/imgs/Pasted%20image%2020250525005723.png)

Para terminar la maquina debemos ser root, por lo tanto buscare binarios.

```
find / -perm -4000 -type f 2>/dev/null
```

![Pasted image 20250525010906.png](/img/user/imgs/Pasted%20image%2020250525010906.png)

Encontramos esos binarios y si usamos la siguiente pagina: https://gtfobins.github.io/gtfobins/env/
- Observamos que le binario env si tiene el bit SUID activado, no elimina los privilegios elevados y puede usarse de forma abusiva para acceder al sistema de archivos, escalar o mantener el acceso privilegiado como una puerta trasera SUID.
Y como verificamos?
- Usaremos lo siguiente:
```
ls -l /usr/bin/env
```

y nos saldra:
```
-rwsr-xr-x 1 root root 43976 Jan  8  2024 /usr/bin/env
```

Ese **s** en lugar de **x** en los permisos de usuarios (rws) significa que el bit SUID esta activado, y env se ejecuta con privilegios de root, aunque tu seas dylan.

Entonces al hacer:
```
env /bin/sh -p
```

1. env: se ejecuta con privilegios de root (por el SUID).
2. env lanza /bin/sh con -p
3. sh -p dice: **no bajes privilegios, mantenme como root**
4. Resultado: una shell con UID 0 -> root

Para entenderlo mejor que es el SUID?
- El SUID es como un disfraz que permite a dylan hacer todo lo que podria hacer un usuario root.
```
-rwsr-xr-x 1 root root 43976 Jan  8  2024 /usr/bin/env
```
Este programa tiene le disfraz magico (SUID) de la autoridad (root).

![Pasted image 20250525015818.png](/img/user/imgs/Pasted%20image%2020250525015818.png)

Finnalmente somos root.

thanks.