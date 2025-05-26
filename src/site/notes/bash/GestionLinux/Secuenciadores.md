---
{"-publish":true,"permalink":"/bash/gestion-linux/secuenciadores/","PassFrontmatter":true}
---


-------------

---------------------

Podemos crear un secuenciador usando seq y añadirle hilos con el comando xargs

```bash
seq 400 2000 | xargs -P 50 -I {}
```

añadiendo otros corchetes donde queramos añadir los hilos