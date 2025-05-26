---
{"-publish":true,"permalink":"/bash/gestion-linux/awk/","PassFrontmatter":true}
---


------------------
#bash #scripting #linux 

-----------------

- Sirve para filtrar los datos por columnas, y lo podemos usar de la siguiente manera 
   -> `cat datos.txt | awk ´{print $1}´` . Esto nos mostraria la primera columna de este archivo.

- Podemos llegar a visualizar directamente la ultima columna sin necesidad de estar contando todas las que hay, y se haria de la siguiente manera ->  `cat datos.txt | awk ´{print $NF}´`
- Podemos llegar a conectar varias columnas con awk, para ello lo podriamos hacer de la siguiente manera ``cat datos.txt | awk ´{print $2 "-->" $6}´``
- Podriamos establecer n delimitador para mostrar las columnas en basae a la separacion por ese delimitador. Podriamos hacerlos asi -> 
```bash
echo 'mario,luis,pedro,jaime' | awk -F ',' '{print $1}'
```

```bash
extensions=$(ls | awk -F '.' '{print $NF}'´)
```
