---
{"-publish":true,"permalink":"/scripting/variables/","PassFrontmatter":true}
---


------------
#bash #scripting #linux #read 

----
- Creamos una variable y la imprimimos con el comando echo y representando la variable con un dolar para que se interprete


```bash
variable='Ruben'
echo $ruben
```

- Si es una string hay que meter el valor entre comillas si no hay strings se hace sin las comillas
```bash
variable='Ruben'
edad=22
echo "Mi nombre es $variable y mi edad es $edad"
```



--------------------------

## Ejecutar un comando dentro de una variable

- Podemos ejecutar un comando y que el resultado de este se almacene en una variable. Para ello podemos usar el comando `variable=$(ls)`
```bash

archivos=$(ls)
ruta=$(pwd)

echo "Vamos a listar todos los archivos de $ruta"

sleep 2

echo $ruta

```

----------

## Comando Read

- Sirve para que podamos capturar el input de un usuario en una variable de la siguiente manera -> `read -p "Introduce tu nombre: " variable`


---------------------------------

## Introduccion de parametros

- Podemos guardar argumentos dentro de una variable desde la ejecucion del script para ellos vamos a utilizar el siguiente script
```bash
nombre=$1
edad=$2
El nombre es igual a $nombre y la edad es $edad
```

Para ejecutar correctamente el script anterior tenemos que ejecutarlo de esta manera ==./script.sh ruben 21.==
- Des esta forma usara esos dos parámetros como los del script

---------------

## Variables especiales

- La primera de todas es la variable ==$?== con la que nosotros vamos a poder saber si el comando ejecutado anteriormente ha sido ejecutado de manera exitosa o no. Si el resultado es 0 el comando ha sido exitoso si el resultado es otro numero diferente sabremos si es erroneo o no.
- La variable ==$#== Vamos a poder saber el numero de argumentos que le vamos a pasar a un script
- La variable $@ Vamos a poder imprimir todos los argumentos que le hem,os pasado a un script