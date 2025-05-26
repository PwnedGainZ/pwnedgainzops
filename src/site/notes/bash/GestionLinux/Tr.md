---
{"-publish":true,"permalink":"/bash/gestion-linux/tr/","PassFrontmatter":true}
---


-------
#bash #scripting #linux 

--------

- Se utiliza para reemplazar caracteres que sean igual en cuanto a su longitud. Por ejemplo en la frase Hola que tal podriamos llegar a sustituir la letra a por un espacio de la siguiente forma -> `echo 'Hola que tal' | tr 'a' ' '`. No podriamos substituir una letra por dos en una misma frase, lo que si que podriamos hacer es modificar dos caracteres por otros dos.
- Podriamos eliminar todos los saltos de linea de una lista paraa que el output estuviese todo junto sustituyendolos por espacios -> `tr '\n' ' '`
- Podemos combertir de mayusculas a minusculas con los siguientes comandos 
```bash
ASLDJÑLAKSJÑDAS | tr '[A-Z]' '[a-z]' 
```