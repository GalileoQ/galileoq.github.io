---
title: "Fowsniff-CTF Writeup - Try-Hack-Me"
date: 2023-10-27
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets/img/imagenes/Fowsniff-CTF.jpeg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Fowsniff-CTF banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>

### nmap
```python
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 90:35:66:f4:c6:d2:95:12:1b:e8:cd:de:aa:4e:03:23 (RSA)
|   256 53:9d:23:67:34:cf:0a:d5:5a:9a:11:74:bd:fd:de:71 (ECDSA)
|_  256 a2:8f:db:ae:9e:3d:c9:e6:a9:ca:03:b1:d7:1b:66:83 (ED25519)
80/tcp  open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Fowsniff Corp - Delivering Solutions
|_http-server-header: Apache/2.4.18 (Ubuntu)
110/tcp open  pop3    Dovecot pop3d
|_pop3-capabilities: TOP RESP-CODES UIDL SASL(PLAIN) PIPELINING AUTH-RESP-CODE CAPA USER
143/tcp open  imap    Dovecot imapd
|_imap-capabilities: Pre-login AUTH=PLAINA0001 more have IDLE post-login listed OK capabilities SASL-IR IMAP4rev1 LITERAL+ LOGIN-REFERRALS ID ENABLE
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 85.59 seconds
```
### Ports: 22/ssh - 80/hhtp - 110/pop3 143/imap

### Enumeracion del puerto 80
![img](/assets/img/imagenes/Pasted image 20231207233234.png)
tenemos un mensaje que nos da informacion sobre un hackeo que ha sufrido la empresa. parece que secuestraron su cuenta de twitter. vamos a mirar

### Twitter
![img](/assets/img/imagenes/Pasted image 20231207233456.png)
vamos a mirar el link

### Informacion sobre el hackeo
![img](/assets/img/imagenes/Pasted image 20231207233636.png)

### backups links
![img](/assets/img/imagenes/Pasted image 20231207233937.png)
tenemos algunos correos electronicos con lo que parece ser el hash

### Fuzzing con gobuster
![img](/assets/img/imagenes/Pasted image 20231207231157.png)
parece que los directorios no tienen nada que sea demasiado importante asi que vamos a decifrar los correos electronicos y los hashes

### usaremos hashid para identificar el tipo de hash de cada uno de los correos electronicos
![img](/assets/img/imagenes/Pasted image 20231208001421.png)
una vez tengamos el tipo de hash podemos decodearlo y asi encontar la contraseña en resumen tenemos un monton de usuarios y un monton de contraseñas que podemos usar para hacer ataques de fuerza bruta

### Fuerza bruta con hydra al puerto 110 
![img](/assets/img/imagenes/Pasted image 20231208001035.png)
![img](/assets/img/imagenes/Pasted image 20231208001115.png)

### entramos al puerto 110/pop3 y tenemos dos archivos debemos mirarlos
![img](/assets/img/imagenes/Pasted image 20231208230437.png)
enumeramos y gardamos todos los nuevos usuarios y las contraseñas a nuestros diccionarios

### hydra por ssh
![img](/assets/img/imagenes/Pasted image 20231208231209.png)

### ssh
![img](/assets/img/imagenes/Pasted image 20231208231402.png)

### Escalada de privilegios
![img](/assets/img/imagenes/Pasted image 20231208235833.png)

### encontramos un archivo que parece ser el estilo que se ejecuta cuando iniciamos session por ssh. el grupo de users puede escribir sobre el y el usuario actual pertenece a ese grupo asi que podemos editar el archivo y poner una reverse shell
![img](/assets/img/imagenes/Pasted image 20231209001421.png)

### guardamos y luego es importante salir del puerto ssh y entrar de nuevo ya que al iniciar session el archivo se ejecuta como root
![img](/assets/img/imagenes/Pasted image 20231209001651.png)

![img](/assets/img/imagenes/Pasted image 20231209001934.png)

### WE ARE ROOT