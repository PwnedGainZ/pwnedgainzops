---
{"-publish":true,"permalink":"/linux/metodos-de-transferencia-linux/","PassFrontmatter":true}
---

## Introducción:

Linux es un sistema operativo versátil, que comúnmente tiene muchas herramientas diferentes que podemos usar para realizar transferencias de archivos. Comprender los métodos de transferencia de archivos en Linux puede ayudar a los atacantes y defensores a mejorar sus habilidades para atacar redes y prevenir ataques sofisticados. 

Hace unos años, nos contactaron para realizar la respuesta a incidentes en algunos servidores web. Encontramos múltiples actores de amenazas en seis de los nueve servidores web que investigamos. El actor de amenazas encontró una vulnerabilidad de inyección SQL. Utilizaron un script Bash que, cuando se ejecutó, intentó descargar otra pieza de malware que se conectaba al servidor de comando y control del actor de amenazas.

El script Bash que utilizaron probó tres métodos de descarga para obtener la otra pieza de malware que se conectaba al servidor de comando y control. Su primer intento fue utilizar `cURL`. Si eso fallaba, intentaba usar `wget`, y si eso fallaba, usaba `Python`. Los tres métodos utilizan HTTP para comunicarse.

Aunque Linux puede comunicarse a través de `FTP`, `SMB` como `Windows`, la mayoría de los programas maliciosos en todos los diferentes sistemas operativos utilizan `HTTP y HTTPS` para la comunicación.

Usaremos múltiples formas de transferir archivos en Linux, incluidos `HTTP`, `Bash`, `SSH`, etc.

![Pasted image 20250613035108.png](/img/user/Pasted%20image%2020250613035108.png)

---

### Descargas web con  Wget y cURL

Dos de las utilidades más comunes en las distribuciones de Linux para interactuar con aplicaciones web son: `Wget` y `cURL`. Estas herramientas están instaladas en muchas distribuciones de Linux.

Para descargar un archivo usando `wget`, necesitamos especificar la `URL` y la opción **'-O'** para establecer el nombre del archivo de salida.

##### Descargar un archivo usando Wget

``` javascript
MrBloody01@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```
`cURL` es muy similar a `wget`, pero la opción de nombre de archivo de salida está en minúsculas **'-o'**. 


##### Descargar un archivo usando cURL

``` JavaScript
MrBloody01@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

---



# Cheatsheet File Transfer

![Pasted image 20250613042024.png](/img/user/Pasted%20image%2020250613042024.png)
![Pasted image 20250613042057.png](/img/user/Pasted%20image%2020250613042057.png)