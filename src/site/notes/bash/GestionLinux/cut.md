---
{"-publish":true,"permalink":"/bash/gestion-linux/cut/","PassFrontmatter":true}
---


--------

------------


- Sirve para poder manipular caracteres, por ejemplo en una frase mostrar las dos primeras letras, la letra numero tres, para poner un ejemplo lo vamos a dejar por aqui -> `echo 'Hola que tal' | cut -c 1` con esto tendriamos la letra H, pero podriamos poner lo mismo añadiendo lo siguiente -> `echo 'Hola que tal' | cut -c 4-1'` Que nos sacaria las letras que hay desde la 4 hasta la 10.

- Tambien podria llegar a filtra por columnas, poniendo una coma en un archivo como delimitador podriamos hacer el siguiente comando. -> `cat archivo.txt | cut -d ',' -f 3 `