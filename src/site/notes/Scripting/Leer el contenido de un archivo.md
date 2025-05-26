---
{"-publish":true,"permalink":"/scripting/leer-el-contenido-de-un-archivo/","PassFrontmatter":true}
---


------------

-----------

- Si tenemos un archivo.txt y queremos leer el contenido que hay dentro y almacenarlo en una variable. Lo podemos hacer de la siguiente manera.

./sctipr.sh archivo.txt

```bash
content=$(<"$1")
```

$1 -> Equivale al archivo que le hemos pasado en la ejecucion de el script