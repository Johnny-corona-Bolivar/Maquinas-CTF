
### Nombre: Agua De Mayo

### 1. ESCANEO

- Podemos identificar el sistema operativo de la maquina, que eso nos podrá ayudar en un futuro a tomar decisiones o buscar vulnerabilidades, para ello ponemos el comando:
  
  
``ping -c 1 172.17.0.2``


- Ahora, hemos de escanear cuales son los puertos abiertos de la maquina, para ello, ejecutamos el siguiente comando:
  
`nmap -p- --open --min-rate=5000 -T5 -A -sT -Pn -n -v 172.17.0.2 ` 



- Ahora, abrimos el navegador, y entramos a la pagina web poniendo como URL (la dirección ip), miramos su código fuente y al final de el todo en la linea 519  aproximadamente hay unas línea que esta en un lenguaje de programación que se llama BRAIN FUCK, usando en internet un programa llamado try it : https://tio.run/# se busca el lenguaje y se pega el codigo y dara una palabra: 
      `bebeaguaqueessano`


- Ahora, con *GOBUSTER* podemos hacer una enumeración de rutas, copiando la URL para indicar que nos busque directorios de esa pagina web, con el siguiente comando:
 ` gobuster dir -u http://172.17.0.2/ -w /usr/share/SecLists/Discovery/web-Content/directory-list-2.3-medium.txt -t 20`

Veremos que hay un directorio que de llama images, lo copiaremos al navegador y veremos que tenemos una imagen que se llama agua_ssh, la descargamos a nuestro ordenador para posteriormente analizarla.
 

- Para ver los metadatos podemos usar varias maneras, una de ellas es ExIFTOOL,

  ``exiftool + ( archivo.jpg )``

- Para alistar caracteres imprimibles que tiene la imagen usaremos STRINGS

  `` strings + ( archivo.jpg ) ``


- Para ver si hay algún archivo oculto en la imagen usamos el siguiente comando:

 `` steghide info agua_ssh.jpg `` YES

### 2. Acceso

- ahora nos conectaremos atraves del SSH, utilizaremos el nombre de la imagen que este en el directorio previamente encontrado. la imagen se llama agua_shh, y utilizaremos la clave que nos dio al momento de resolver el codigo brain fuck:  bebeaguaqueessano

      ``ssh agua@172.17.0.2 al pedir la contraseña usamos bebeaguaqueessano


- Una vez con acceso tenemos que elevar privilegios, para ello podemos hace un `` sudo -l`` para ver que comandos puede ejecutar este usuario. Vemos que tenemos un binario que se llama bettercap que podemos ejecutar, por lo tanto ponemos `` sudo bettercap``

- Podemos ver poniendo help como el índice de el modulo y vemos que para usar comandos hay que usar la exclamación, por ejemplo:
`` ! COMMAND : Execute a shell command and print its output.``

-   hacemos exit: veificamos los permisos SUID que hay con el comando ``find / -perm -4000 2> /dev/null`` , observaremos que hay diferentes directorios y nos enfocaremos en el ``/usr/bin/bash`` ya que tiene una bash que podemos darnos privilegios.

- Nos daremos permisos a la bash para poder ser root `` ! chmod u+s /usr/bin/bash
  hacemos exit y ejecutamos
- bash -p
- whoami
