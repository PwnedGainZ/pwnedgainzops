---
{"-publish":true,"permalink":"/scripting/condicionales/","PassFrontmatter":true}
---


--------------
#scripting #bash #linux 

----------

Este seria un ejemplo de una condicion basica en bash

```bash
fruta=naranja

if [ "$fruta"="$naranja" ]; then
		 echo "La variable fruta es igual a naranja"
fi
```

```bash
fruta=naranja

if [ "$fruta"="$naranja" ]; then
		 echo "La variable fruta es igual a naranja"
elif [ "$fruta" = "$manza"]; then
		 echo "La variable fruta es igual a manzana"
fi
```

```bash
fruta=naranja

if [ "$fruta"="$naranja" ]; then
		 echo "La variable fruta es igual a naranja"
elif [ "$fruta" = "$manza"]; then
		 echo "La variable fruta es igual a manzana"
else
	 echo "No se detecto el patron dentro de la variable fruta"
fi
```