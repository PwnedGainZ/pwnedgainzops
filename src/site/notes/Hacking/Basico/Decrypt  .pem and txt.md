---
{"-publish":true,"permalink":"/hacking/basico/decrypt-pem-and-txt/","PassFrontmatter":true}
---


## Decrypt .pem and txt
--------------------------

---------------------------------

- Podemos desencriptar una archivo pem si tenemos un txt de la siguiente forma.

```bash
openssl pkeyutl -decrypt -inkey private_key.pem -in private.txt -out decrypted_file.txt
```

![Pasted image 20240526173531.png](/img/user/imgs/Pasted%20image%2020240526173531.png)