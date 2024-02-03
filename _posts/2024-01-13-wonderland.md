---
title: "Wonderland Writeup - Try-Hack-Me"
date: 2024-01-13
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Medium]
image: 
  path: assets/img/imagenes/Wonderland.jpeg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Wonderland banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### nmap
```python
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 84.27 seconds
```

### Ports: 22/ssh - 80/http

### Enumeración del puerto 80
no tenemos mucha informacion en la pagina asi que vamos a realizar fuzzing
![img](/assets/img/imagenes/Pasted image 20240113203037.png)

### Fuzzing con gobuster
tenemos algunos directorios que vamos a investigar 
![img](/assets/img/imagenes/Pasted image 20240113203227.png)

### /img/

![img](/assets/img/imagenes/Pasted image 20240113203353.png)

### /r/

![img](/assets/img/imagenes/Pasted image 20240113203444.png)

### /poem/

![img](/assets/img/imagenes/Pasted image 20240113203709.png)

### parece que el mas interesante es el directorio /r/ así que seguiremos investigando por aquí. haremos un nuevo ataque de fuzzing apuntando a este directorio

![img](/assets/img/imagenes/Pasted image 20240113203946.png)

### tenemos un nuevo directorio que agregaremos a la ruta y nos quedaria de la siguiente manera http://10.10.63.231/r/a/

![img](/assets/img/imagenes/Pasted image 20240113204104.png)
tenemos el mismo mensaje asi que seguiremos haciendo fuzzing

### la ruta rotal nos lleva al directorio http://10.10.63.231/r/a/b/b/i/t/

![img](/assets/img/imagenes/Pasted image 20240113204522.png)

### miramos el código fuente y conseguimos información que parece ser credenciales

![img](/assets/img/imagenes/Pasted image 20240113204827.png)

### extraemos informacion de las imagenes 
una de las imagenes contiene un archivo.txt pero no es de mucha informacion
![img](/assets/img/imagenes/Pasted image 20240113205534.png)

### Port 22/ssh
vamos a intentar ingresar al puerto ssh con la inofrmacion encontrada en el codigo fuente de la pagina web
![img](/assets/img/imagenes/Pasted image 20240113205819.png)
tenemos conexion como el usuario alice

### Escalada de privilegios 

![img](/assets/img/imagenes/Pasted image 20240113210239.png)
el usuario rabbit tiene permiso para ejecutar el siguiente binario

### el archivo importa la libreria ramdon de python

![img](/assets/img/imagenes/Pasted image 20240113212411.png)
usaremos esto para realizar una especia de hijacking. basicamente tenemos la ruta donde se esta ejecutando el script asi que si creamos un archivo llamado random el script llamara primero a este archivo antes que a la libreria 

### primero crearemos un archivo llamado random.py

![img](/assets/img/imagenes/Pasted image 20240113213035.png)

### ejecutaremos el script para cambiar de usuario

```python
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

![img](/assets/img/imagenes/Pasted image 20240113213312.png)
logramos cambiar de usuario a rabbit

### vamos al directorio del usuario rabbit

![img](/assets/img/imagenes/Pasted image 20240113213525.png)
tenemos otro 

### PATH hijacking 

![img](/assets/img/imagenes/Pasted image 20240113214522.png)
el escript utiliza la variable date de linux asi que podemos utilizar esto para crear un archivo llamado date y poder agregarlo en el path

### creamos el archivo date 
le damos permisos de lectura al archivo
![img](/assets/img/imagenes/Pasted image 20240113215245.png)

### agregamos date al PATH

![img](/assets/img/imagenes/Pasted image 20240113215115.png)

### ejecutamos el script 

![img](/assets/img/imagenes/Pasted image 20240113220101.png)
hemos cambiado de usuario exitosamente

### Escalada de privilegios
enumeracion de capabilitis
![img](/assets/img/imagenes/Pasted image 20240113225005.png)
tenemos el binario perl el cual usaremos para escalar privilegios

```python
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

![img](/assets/img/imagenes/Pasted image 20240113225241.png)

### WE ARE ROOT
