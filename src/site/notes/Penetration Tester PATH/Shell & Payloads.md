---
{"-publish":true,"permalink":"/penetration-tester-path/shell-and-payloads/","PassFrontmatter":true}
---


# Los Shells nos introducen, los Payloads nos entregan Shells

Un `Shell`, es un programa que proporciona a un usuario de computadora una interfaz para ingresar instrucciones en el sistema y ver la salida de texto **(Bash,Zsh,CMD & PowerShell, por ejemplo).** Como Pentesters y profesionales de Sistemas de la Información, un Shell suele ser el resultado de explotar una  vulnerabilidad o eludir las medidas de seguridad para obtener `acceso interactivo a un host`. Es posible que hayamos escuchado o leído las siguientes frases utilizadas por las personas que hablan sobre un compromiso o una sesión de práctica reciente:

![Pasted image 20250615034335.png](/img/user/Pasted%20image%2020250615034335.png)

---
## Por qué obtener un Shell?

Recordemos que un Shell nos da acceso directo de manera interactiva al `OS`, `comandos de sistema` & `sistema de archivos`.
Entonces si obtenemos acceso, podemos comenzar a enumerar el sistema en busca de vectores que nos permitan escalar privilegios, pivotar, transferir archivos y más. Si no establecemos una sesión de Shell, estamos bastante limitados en cuanto a lo que podemos llegar en la máquina objetivo.

Establecer una Shell, nos permite mantener `persistencia` en el sistema, a lo cuál nos da más tiempo para trabajar. Puede facilitar el uso de nuestras `Herramientas de ataque`, `exfiltrar datos`, `recoletar`, `guardar` y `documentar` todos los detalles de nuestro ataque, como pronto veremos en las demostraciones que siguen. Es importante tener en cuenta que establecer un Shell casi siempre significa que estamos accediendo a la `CLI` del sistema operativo, y esto puede hacer que sea más difícil de notar que si estuviéramos accediendo de forma remota a un Shell gráfico a través de `VNC` o `RDP`. Otro beneficio significativo de convertirse en experto con las interfaces de línea de comandos es que pueden ser `harder to detect than graphical Shells` `faster to navigate the OS` & `easier to aumate our actions`. Veremos las Shells a través de la lente de las siguientes perspectivas:

![Pasted image 20250615035811.png](/img/user/Pasted%20image%2020250615035811.png)

---

### Los Payloads nos entregan Shells

Dentro de la industria de TI, un `payload` se puede definir de diferentes maneras:

- `Networking`: La porción de datos encapsulada de un paquete que atraviesa las redes informáticas modernas.
- `Basic Computing`: Un payload es la parte de un conjunto de instrucciones que define la acción que se va a realizar. Se han eliminado los encabezados y la información del protocolo.
- `Programming`: La parte de los datos a la que se hace referencia o que transporta la instrucción del lenguaje de programación.
- `Exploitation & Security`: Un payload es `código` diseñado con la intención de explotar una vulnerabilidad en un sistema informático. El término **"payload"** puede describir varios tipos de malware, incluido, entre otros, el Ransomware.

---

# Cheatsheet Shells & Payloads

![Pasted image 20250615041255.png](/img/user/Pasted%20image%2020250615041255.png)
![Pasted image 20250615041331.png](/img/user/Pasted%20image%2020250615041331.png)
![Pasted image 20250615041345.png](/img/user/Pasted%20image%2020250615041345.png)
![Pasted image 20250615041406.png](/img/user/Pasted%20image%2020250615041406.png)
![Pasted image 20250615041419.png](/img/user/Pasted%20image%2020250615041419.png)
![Pasted image 20250615041428.png](/img/user/Pasted%20image%2020250615041428.png)

---
#payloads #shells

---