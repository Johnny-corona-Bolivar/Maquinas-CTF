## Breaking RSA

A brief overview of RSA

The security of RSA relies on the practical difficulty of factoring the product of two large prime numbers, the "factoring problem". RSA key pair is generated using 3 large positive integers 



 -How many services are running on the box?
R:2
    se usa nmap para poder escanear la ip y observar cuantos servicios corren,


- What is the name of the hidden directory on the web server? (without leading '/')
     con gobuster hacemos un escaneo para encontrar los directorios ocultos.
     R: development
	 ```bash
 gobuster dir -u <directorio> -w /usr/share/wordlist/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt o usamos rockyou
```

una vez con el directorio oculto observamos que hay dos documentos, uno llamado id_rsa.pub y otro llamado log.txt, procedemos a descargar el is_rsa.pub.

- What is the length of the discovered RSA key? (in bits)

	 una vez descargado usamos:
 ``` bash
 ssh-keygen -l -f id_rsa.pub
```
y observadoremos el length: 4096


- What are the last 10 digits of n? (where 'n' is the modulus for the public-private key pair)
	 para esta parte usaremos un script de python desde github llamado RSA_breaker 
     https://github.com/kali-mx/RSA_breaker.git

Procedemos a correr el script python3 RSA_breaker.py , luego procederemos a introducir la ip para poder romperlo.
el script nos generara una llave privada llamada: ssh_private_key.pem
procederemos luego a ejecutar:
ssh -i <nombre de la clave privada> root@ip (root porque en el documento log.txt aparece el root enable).

Y estariamos adentro solo abria que catear el flag que aparece.
en el mismo proceso cuando corremos el script nos da el valor de .

 - What is the numerical difference between p and q? : 1502
 - What are the last 10 digits of n? (where 'n' is the modulus for the public-private key pair) : 1225222383
 - What is the flag? : breakingRSAissuperfun20220809134031
