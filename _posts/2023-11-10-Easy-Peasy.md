---
title: "Easy-Peasy Writeup - Try-Hack-Me"
date: 2023-11-10
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets/img/imagenes/Easy-Peasy.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Easy-Peasy banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### nmap 

```python
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: nginx/1.16.1
|_http-title: Welcome to nginx!
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 30:4a:2b:22:ac:d9:56:09:f2:da:12:20:57:f4:6c:d4 (RSA)
|   256 bf:86:c9:c7:b7:ef:8c:8b:b9:94:ae:01:88:c0:85:4d (ECDSA)
|_  256 a1:72:ef:6c:81:29:13:ef:5a:6c:24:03:4c:fe:3d:0b (ED25519)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
|_http-server-header: Apache/2.4.43 (Ubuntu)
|_http-title: Apache2 Debian Default Page: It works
| http-robots.txt: 1 disallowed entry 
|_/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds
```

### Ports: 80/http - 6498/ssh - 65524/http

### Enumeracion del puerto 80/65524

![img](/assets/img/imagenes/Pasted image 20231206201035.png)

![img](/assets/img/imagenes/Pasted image 20231206201231.png)

### en el codigo fuente de la maquina haciendo redireccion al puerto 65524 tenemos algo oculto
![img](/assets/img/imagenes/Pasted image 20231206201418.png)

### vamos a desencriptar este codigo el hash analizer nos indica que esta en base62 asi que vamos a decodear

![img](/assets/img/imagenes/Pasted image 20231206202254.png)

### encontramos lo que parece ser un sub directorio,  investigamos y tenemos mas informacion
vamos a descargar la imagen
![img](/assets/img/imagenes/Pasted image 20231206204807.png)

### tenemos otro codigo que podeos decencriptar 

![img](/assets/img/imagenes/Pasted image 20231206205652.png)

### extraemos la informacion oculta en la imagen y utilizamos esta password 
![img](/assets/img/imagenes/Pasted image 20231206213831.png)

### este archivo nos entrega el usuario boring un codigo binario

![img](/assets/img/imagenes/Pasted image 20231206225142.png)

### nuestro escaneo de nmap nos dice que en el puerto 65524 esta un recurso compartido llamado robots.txt
aca tenemos la primera flag
![img](/assets/img/imagenes/Pasted image 20231206232632.png)

### conseguimos la flag 1 en los dos directorios que hemos encontrado con gobuster
solo tenemos que decodearla
![img](/assets/img/imagenes/Pasted image 20231206233837.png)

### conseguimos la flag 2 en el archivo robots que se esta compartiendo en el puerto 65524
tenemos que decodearla
![img](/assets/img/imagenes/Pasted image 20231206234057.png)

### conseguimos la flag 3 en el codigo fuente del puerto 65524
tenemos que decodear
![img](/assets/img/imagenes/Pasted image 20231206232830.png)

### Enumeracion del puerto 6498/ssh

![img](/assets/img/imagenes/Pasted image 20231206234950.png)
### user flag
![img](/assets/img/imagenes/Pasted image 20231206235224.png)

### Escalada de privilegios

![img](/assets/img/imagenes/Pasted image 20231207000504.png)

### tenemos una tarea crontab en la que tenemos permisos de ejecusion

![img](/assets/img/imagenes/Pasted image 20231207001157.png)
hacemos ls y tenemos permisos para editar esta tarea crontab 

### editamos los permisos de la bash
![img](/assets/img/imagenes/Pasted image 20231207001318.png)

### ejecutamos bash -p 
![img](/assets/img/imagenes/Pasted image 20231207001407.png)
### WE ARE ROOT