---
{"-publish":true,"permalink":"/penetration-tester-path/ataques-a-contrasenas/","PassFrontmatter":true}
---


## Introducción

`Confidencialidad`, `Integridad`, `Disponibilidad` están en el centro de responsabilidades de todo profesional de la seguridad de la información. Si no mantenemos un equilibrio entre ellos, no podemos garantizar la seguridad de nuestras empresas. Este equilibrio se conserva mediante la auditoría y la contabilidad de cada archivo, objeto y host en el entorno. La mayoría de las violaciones se remontan a la ruptura de uno de estos tres principios. Este módulo se centra en atacar y eludir el principio de `Autenticación` comprometiendo las contraseñas de los usuarios en varios sistemas operativos, aplicaciones y métodos de cifrado.

### Autenticación

La autenticación, en esencia, es la validación de su identidad mediante la presentación de una combinación de cuatro factores a un mecanismo de validación.

- `Algo que sabes`: una contraseña, código de acceso, PIN, etc.
- `Algo que tienes`: una tarjeta de identificación, una clave de seguridad u otras herramientas de MFA.
- `Algo que eres`: su yo físico, nombre de usuario, dirección de correo electrónico u otros identificadores
- `Somewhere you are`: geolocalización, dirección IP, etc.

### El uso de contraseñas

El método de autenticación más común y ampliamente utilizado sigue siendo el uso de contraseñas. Una contraseña o frase de contraseña se puede definir generalmente como `una combinación de letras, números y una cadena símbolos para una validación de identidad`. Por ejemplo, si trabajamos con contraseñas y tomamos una contraseña estándar de 8 dígitos que consta solo de letras mayúsculas y números.  

Ahora que sabemos esto, pasamos a una sección importante la cuál se es introducción al crackeo de contraseñas.

---
## Introducción al crackeo de contraseñas

Las contraseñas son comúnmente `hashed` cuando se almacenan, con el fin de proporcionar cierta protección en caso de que caigan en manos de un atacante. `Hashing` es una función matemática que transforma un número arbitrario de bytes de entrada en una salida (típicamente) de tamaño fijo; ejemplos comunes de funciones hash son `MD5` y `SHA-256`.

``` shell
bmdyy@htb:~$ echo -n Soccer06! | md5sum
40291c1d19ee11a7df8495c4cccefdfa -
```

Las funciones hash están diseñadas para funcionar en `one direction`. Esto significa que no debería ser posible averiguar cuál era la contraseña original basándose únicamente en el hash. Cuando los atacantes intentan hacer esto, se llama `password cracking`. Las técnicas comunes son usar `rainbow tables`, para realizar `dictionary attacks` y, por lo general, como último recurso, para realizar `brute-force attacks`.

## Tablas Rainbow

Las tablas Rainbow son grandes mapas pre-compilados de valores de entrada y salida para una función hash determinada. Estos se pueden usar para identificar muy rápidamente la contraseña si ya se ha mapeado su hash correspondiente.

![Pasted image 20250617150609.png](/img/user/Pasted%20image%2020250617150609.png)

Debido a que las mesas Rainbow son un ataque tan poderoso, `salting` se utiliza. Un `salt`, en términos criptográficos, es una secuencia aleatoria de bytes agregados contraseña antes de que se le agregue un hash. Para maximizar el impacto, las sales no deben reutilizarse, por ejemplo, para todos las contraseñas almacenadas en una base de datos. Por ejemplo, si la sal `Th1sIsTh3s@lt_` se antepone a la misma contraseña, el hash MD5 ahora sería el siguiente:

![Pasted image 20250617151029.png](/img/user/Pasted%20image%2020250617151029.png)

Un Salt no es un valor secreto: cuando un sistema va a verificar una solicitud de autenticación, necesita saber qué sal se usó para poder verificar si el hash de la contraseña coincide. Por esta razón, las sales suelen ir precedidas de los hashes correspondientes. La razón por la que se esta técnica funciona con las tablas Rainbow es que incluso si se ha asignado contraseña correcta, es probable que la combinación de sal y contraseña no lo haya hecho (especialmente si la sal contiene caracteres no imprimibles).

---
## Ataque de fuerza bruta

Un `ataque de fuerza bruta` consiste en intentar las combinaciones posibles de letras, números y símbolos hasta que se descubra la contraseña correcta. Obviamente, esto puede llevar mucho tiempo, número y símbolos hasta que se descubra la contraseña correcta.

### Tipos de hash

Hashcat admite cientos de tipos de hash diferentes, a cada uno de los cuales se le asigna un ID. Se puede generar una lista de identificadores asociados ejecutando `hashcat` `--help`.

![Pasted image 20250617171052.png](/img/user/Pasted%20image%2020250617171052.png)

El sitio web de `hashcat` alberga una lista completa de [hashes de ejemplo](https://hashcat.net/wiki/doku.php?id=example_hashes) que pueden ayudar a identificar manualmente un tipo de hash desconocido y determinar el identificador de modo hash de Hashcat correspondiente.

Alternativamente, [hashID](https://github.com/psypanda/hashID) se puede usar para identificar rápidamente el tipo de hash hashcat especificando el `-m` argumento.

![Pasted image 20250617171846.png](/img/user/Pasted%20image%2020250617171846.png)

## Modos de ataque

Hashcat tiene muchos modos de ataque diferentes, que incluyen `diccionario`, `máscara`, `combinado`, `asociado`. En esta sección repasaremos los dos primeros, ya que probablemente sean los más comunes que necesitará usar.

##### Ataque de diccionario

Ataque de diccionario (`-a 0`), es cuando el usuario proporciona hashes de contraseña y una lista de palabras como entrada, y Hashcat prueba cada palabra de la lista como una contraseña potencial hasta que se encuentra la correcta o se agota la lista.

![Pasted image 20250617173735.png](/img/user/Pasted%20image%2020250617173735.png)

Una lista de palabras por sí sola a menudo no es suficiente para descifrar un hash de contraseña. Al igual que en el caso de JtR, `rules` Se puede utilizar para realizar modificaciones específicas en las contraseñas para generar aún más conjeturas. Los archivos de reglas que vienen con hashcat generalmente se encuentran en `/usr/share/hashcat/rules`:

![Pasted image 20250617174334.png](/img/user/Pasted%20image%2020250617174334.png)

![Pasted image 20250617174643.png](/img/user/Pasted%20image%2020250617174643.png)

## Ataque de máscara

Ataque de máscara (-a 3) es un tipo de ataque de fuerza bruta en el que el espacio de claves es definido explícitamente por el usuario. Por ejemplo, si sabemos que una contraseña tiene ocho caracteres, en lugar de intentar todas las combinaciones posibles, podríamos definir una máscara que pruebe combinaciones de seis letras seguidas de dos números.

![Pasted image 20250617175543.png](/img/user/Pasted%20image%2020250617175543.png)

---

## Escribiendo lista de palabras y reglas personalizadas

Muchos usuarios crean sus contraseñas basándose en `simplicidad en lugar de seguridad `, para ello, se pueden implementar políticas de contraseñas en los sistemas para hacer cumplir requisitos específicos de contraseñas. Por ejemplo, un sistema podría imponer la inclusión de letras mayúsculas, caracteres especiales y números.

Desafortunadamente, la tendencia de los usuarios a crear contraseñas débiles ocurre incluso cuando existen políticas de contraseñas. La mayoría de las personas siguen patrones predecibles al crear contraseñas, a menudo incorporando palabras estrechamente relacionadas con el servicio al que se accede. Por ejemplo, muchos empleados eligen contraseñas que incluyen el nombre de la empresa. Las preferencias e intereses personales también juegan un papel importante. Debido a que puede incluir información personal relacionadas a su vida cotidiana. Las técnicas básicas de OSINT (Open Source Intelligence) pueden ser muy eficaces para descubrir dicha información personal y pueden ayudar a adivinar las contraseñas.

![Pasted image 20250617182103.png](/img/user/Pasted%20image%2020250617182103.png)

---

![Pasted image 20250617182122.png](/img/user/Pasted%20image%2020250617182122.png)

Cada regla se escribe en una nueva línea y determina cómo se debe transformar una palabra. Si escribimos las funciones mostradas anteriormente en un archivo, puede verse así:

``` sh
MrBloody01@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```


# Generación de listas de palabras mediante CeWL

Podemos usar una herramienta llamada **CeWL** para escanear palabras potenciales del sitio web de una empresa y guardarlas en una lista de contraseñas personalizada, una que tenga una mayor probabilidad de contener la contraseña correcta de un empleado. Especificamos algunos parámetros, como la profundidad to spider (`-d`), la longitud mínima de la palabra (`-m`), el almacenamiento de las palabras encontradas en minúsculas (`--lowercase`), así como el fichero donde queremos almacenar los resultados (`-w`).

![Pasted image 20250617183101.png](/img/user/Pasted%20image%2020250617183101.png)


### Spraying, Stuffing, and Defaults

#### Password Spraying

El `password spraying` es un tipo de ataque de fuerza bruta en el que un atacante intenta usar una sola contraseña en muchas cuenta de usuario diferentes. Por ejemplo, si se sabe que los administradores de una empresa en particular suelen utilizar `ChangeMe123!` al configurar nuevas cuentas, valdría la pena rociar esta contraseña en todas las cuentas de `usuario` para identificar las que no se actualizaron.

![Pasted image 20250618170411.png](/img/user/Pasted%20image%2020250618170411.png)

##### Relleno de credenciales

Es otro tipo de ataque de fuerza bruta en el que un atacante utiliza credenciales robadas de un servicio para intentar acceder a otros. Dado que `muchos usuarios reutilizan sus nombres de usuario y passwords en múltiples plataformas` (como el email, las redes sociales y los sistemas empresariales). Por ejemplo, si tenemos una lista de `username:password` obtenidas de una fuga de base de datos, podemos usar `hydra` para realizar un ataque de relleno de credenciales contra un servicio SSH utilizando la siguiente sintaxis:

![Pasted image 20250618170817.png](/img/user/Pasted%20image%2020250618170817.png)

##### Credenciales default

Muchos sistemas, como enrutadores, firewalls y DB, vienen con `default credentials`. Si bien las mejores prácticas dictan que los administradores cambien estas credenciales durante la configuración, a veces se dejan sin cambios, lo que representa un grave riesgo de seguridad.

Hay varias listas de credenciales predeterminadas conocidas disponibles en internet, pero, también hay herramientas dedicadas que automatizan el proceso. Un ejemplo de ello es la [default credentials cheat sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet) , que podemos instalar con `pip3`.

![Pasted image 20250618171514.png](/img/user/Pasted%20image%2020250618171514.png)

Una vez instalado, podemos usar el método `creds` para buscar credenciales predeterminadas conocidas asociadas con un producto o proveedor específico.

![Pasted image 20250618171756.png](/img/user/Pasted%20image%2020250618171756.png)

Imaginemos que hemos identificado ciertas aplicaciones en uso en la red de un cliente. Después de investigar las credenciales predeterminadas en línea, podemos combinarlas en una nueva lista, con el formato `username:password`, y reutilizar el anteriormente mencionado `hydra` para intentar acceder.

![Pasted image 20250618171923.png](/img/user/Pasted%20image%2020250618171923.png)



---
#password #hash

---


