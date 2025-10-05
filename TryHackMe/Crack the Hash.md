## Crack the Hash

Can you complete the level 1 tasks by cracking the hashes?

#### Level 1

- Mayormente los hash presentado se pueden crackear son hashes online.
como crackstation.

48bb6e862e54f2a795ffc4e541caed4d :
CBFDAC6008F9CAB4083784CBD1874F76618D2A97: password123
1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032 :letmein


4- $2y $12$ Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom : 
usaremos hashid -m " \$2y\$12\$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom"
nos dara el identificador que seria  3200 o podemos buscar en internet tomando solo el inicio del hash
y podemos usar hashcat -m 3200 <hash> /opt/rockyou.txt (directo desde la maquina)
o podemos guardar el archivo en un txt y romperlo luego R: bleh

Tambien podemos usar hash-idetifier

279412f945939ba78ce0758d3fd83daa: Eternity22




LEVEL 2

Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85 : paule
Hash: 1DFECA0C002AE40B8619ECF94819CC1B : n63umy8lkf4i

Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

en este caso podemos usar hash-id "\$6\$(resto del hash)" para obtener el identificador o en la lista de identificadores podemos conseguirlo.
procedemos a guardar el archivo en un txt y a descifrarlo con rockyou.txt
hashcat -m 1800 hash.txt <rutadelrockyou.txt> R: waka99


Hash: e5d8870e5bdd26602cab8dbe07a942c8669e56d6: 481616481616
agregamos el salt de la siguiente manera:
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme y lo guardamos en un txt, antes procederemos a buscar en hash-identifier el identificador o usamos hash-id -m e5d8870e5bdd26602cab8dbe07a942c8669e56d6,  para ver que codigo usar seria 160
procedemos: hashcat -m 1800 hash.txt <rutadelrockyou.txt>

