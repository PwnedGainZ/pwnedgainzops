---
{"-publish":true,"permalink":"/penetration-tester-path/metasploit/","PassFrontmatter":true}
---


## Preface

Las herramientas han sido objeto de acalorados debates dentro de los círculos de redes sociales de la industria de la seguridad. Algunas discusiones giraron en torno a las preferencias personales de algunos grupos. Sin embargo, es necesario señalar la importancia de las herramientas automatizadas en la industria actual.

Otro grupo vocal no está de acuerdo: los que están formados por miembros más nuevos de la comunidad de seguridad de la información, que recién están comenzando y dando sus primeros pasos, y aquellos que *sostienen el argumento de que las herramientas nos ayudan a aprender mejor* al ofrecernos un enfoque más fácil de usar para la gran cantidad de vulnerabilidades que existen en la naturaleza, al `tiempo` que nos ahorran para las partes más intrincadas de una evaluación. 

De hecho, las herramientas pueden, en algunos casos, presentarnos algunas desventajas:

- Crear una zona de confort de la que sea difícil salir para aprender nuevas habilidades
- Cree un riesgo de seguridad solo porque se publican en línea para que todos los vean y usen
- Crea un efecto de visión de túnel. `Si la herramienta no puede hacerlo, yo tampoco puedo`

---
## Discipline

Si hay algún factor de discernimiento que se puede extraer del estado actual de la industria de la seguridad de la información, debe extraerse de la premisa de que estamos en una evolución continua y acelerada de la tecnologías, protocolos y sistemas existentes. Con el cúmulo de variables del entorno que encontramos durante una evaluación. La `disciplina es fundamental` en todos los campos de trabajo.


| Nunca tendremos tiempo suficiente para completar la evaluación. Con la cantidad de tecnologías en uso en cada variación del entorno, no sen os ofrecerá el tiempo para hacer una evaluación competa y exhaustiva. El tiempo es DINERO, y estamos en el reloj para un cliente que no es experto en tecnología, y primero debemos completar la mayor parte del trabajo: los problemas con el mayor IMPACTO potencial y la mayor rotación de remediación.                                                               |     |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| La credibilidad puede ser un problema incluso si fabricamos nuestras herramientas o explotamos manualmente cada servicio. **No estamos compitiendo contra otros miembros de la industria, sino más bien contra condiciones económicas preestablecidas y creencias personales desde el nivel de gestión del cliente**. No comprendían ni daban mucha importancia a los elogios. Solo quieren que el trabajo se haga en la mayor cantidad posible, en la menor cantidad de tiempo.                                     |     |
| Solo **tienes que impresionarte a ti mismo**, no a la comunidad de seguridad de la información. **Si conseguimos lo primero, lo segundo vendrá de forma natural**. Usando el mismo ejemplo que el anterior, muchos artistas con presencia en línea se desvían de sus objetivos originales en busca de validación en línea. Su arte se vuelve obsoleto y genérico para el ojo agudo, pero para el usuario cotidiano, contiene los elementos visuales y temas deseados, no aquellos que sus seguidores aún no quieren. |     |

---

## Conclution

Tenemos que analizar y conocer nuestras herramientas por dentro y por fuera para mantener nuestras huellas cubiertas y evitar un evento cataclísmico durante nuestra evaluación. Muchas herramientas pueden resultar impredecibles. Algunos pueden dejar rastros de actividad en el sistema objetivo, y otros pueden dejar nuestra plataforma de ataque con puertas abiertas. Sin embargo, siempre que sigamos las reglas aquí, pueden ser una valiosa plataforma educativa para principiantes y un mecanismo de ahorro de tiempo necesario para los profesionales.

NO TENGA VISIÓN DE TÚNEL. **Utilicemos la herramienta como una herramienta**, ==no como una columna vertebral o soporte vital para nuestra evaluación completa==.


---

# Introduction to Metasploit

El proyecto `Metasploit` es una plataforma modular de pruebas de penetración basada en Ruby lo cuál le permite escribir, probar y ejecutar el código de explotación. Este código de `exploit` puede ser personalizado por el usuario o tomado de una base de datos que contiene los últimos exploits ya descubiertos y modularizados. El **framework** `Metasploit` incluye un conjunto de herramientas que puede utilizar para probar vulnerabilidades de seguridad, enumerar redes, ejecutar ataques y evadir la detección. ***Entonces, el proyecto Metasploit es una colección de herramientas de uso común que proporcionan un entorno completo para las pruebas de penetración y el desarrollo de exploits.***

![Pasted image 20250616074134.png](/img/user/Pasted%20image%2020250616074134.png)

Su punto fuerte es que proporciona una gran cantidad de objetivos y versiones disponibles, todos a pocos comandos de distancia de un punto de apoyo exitoso. Estos, combinados con un exploit hecho a medida para esas versiones vulnerables y con una carga útil que se envía después del exploit, que nos dará acceso real al sistema.

### Metasploit Pro

Metasploit como producto se divide en dos versiones. El `Metasploit Pro` y el `Metasploit Framework` uno con algunas características adicionales:

- Cadenas de tareas
- Ingeniería Social
- Validaciones de vulnerabilidades
- Interfaz gráfica de usuario
- Asistentes de inicio rápido
- Integración de Nexpose

Si eres más un usuario de línea de comandos y prefieres las funciones adicionales, la versión Pro también cuenta con su propia consola, al igual que `msfconsole`.

![Pasted image 20250616075114.png](/img/user/Pasted%20image%2020250616075114.png)

## Modules

Los módulos se dividen en categorías separadas en esta carpeta. Entraremos en detalle sobre estos en las siguientes secciones. Están contenidos en las siguientes carpetas:

``` bash
MrBloody01@htb[/htb]$ ls /usr/share/metasploit-framework/modules

auxiliary enconders evasion exploits nops payloads post
```

## Complements

Los plugins ofrecen al Pentester más flexibilidad a la hora de utilizar el `msfconsole` ya que se pueden cargar fácilmente de forma manual o automática según sea necesario para proporcionar funcionalidad y automatización adicionales durante nuestra evaluación.

![Pasted image 20250616075724.png](/img/user/Pasted%20image%2020250616075724.png)

## Scripts

Funcionalidad de `Meterpreter` y otros scripts útiles.

![Pasted image 20250616075819.png](/img/user/Pasted%20image%2020250616075819.png)

## Tools

Utilidades de línea de comandos a las que se puede llamar directamente desde el menú `msfconsole`

![Pasted image 20250616075928.png](/img/user/Pasted%20image%2020250616075928.png)

Ahora que conocemos todas estas ubicaciones, nos será fácil hacer referencia a ellas en el futuro cuando decidamos importar nuevos módulos o incluso crear nuevos desde cero :)

---
#metasploit #msfconsole #discipline

---

