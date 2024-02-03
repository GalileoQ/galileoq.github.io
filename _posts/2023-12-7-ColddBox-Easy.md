---
title: "ColddBox-Easy Writeup - Try-Hack-Me"
date: 2023-12-07
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets\img\imagenes\ColddBox-Easy.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "ColddBox-Easy banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### nmap

```python
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-06 11:52 EST
Nmap scan report for 10.10.35.246
Host is up (0.21s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.1.31
|_http-title: ColddBox | One more machine
|_http-server-header: Apache/2.4.18 (Ubuntu)
4512/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4e:bf:98:c0:9b:c5:36:80:8c:96:e8:96:95:65:97:3b (RSA)
|   256 88:17:f1:a8:44:f7:f8:06:2f:d3:4f:73:32:98:c7:c5 (ECDSA)
|_  256 f2:fc:6c:75:08:20:b1:b2:51:2d:94:d6:94:d7:51:4f (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 75.77 seconds
```
### Enumeracion del puerto 80

![img](/assets/img/imagenes/Pasted image 20240106131915.png)

### utilizaremos la eramienta wpscan

![img](/assets/img/imagenes/Pasted image 20240106132000.png)
conseguimos uun posible archivo XML

### usuarios

![img](/assets/img/imagenes/Pasted image 20240106132420.png)
tenemos al usuario philip, c0ldd, hugo. creamos un pequeño diccionario con estos usuarios y lo usaremos para hacer fuerza bruta
![img](/assets/img/imagenes/Pasted image 20240106132913.png)

![img](/assets/img/imagenes/Pasted image 20240106133114.png)

### aqui estamos haciendo un ataque de fueraza bruta para conseguir la contraseña de alguno de los usuarios 
Conseguimos la contraseña
![img](/assets/img/imagenes/Pasted image 20240106133342.png)

### Credenciales

```python
users: c0ldd
passw: 9876543210
```
### ingresamos al wordpress

![img](/assets/img/imagenes/Pasted image 20240106133706.png)

### vamos al apartado de themas y podemos editar un thema 

![img](/assets/img/imagenes/Pasted image 20240106134008.png)
aqui vamos a inyectar comando en php
![img](/assets/img/imagenes/Pasted image 20240106135230.png)

### inyectamos nuestro codigo php y buscamos la ruta exacta donde se guarda esta template

![img](/assets/img/imagenes/Pasted image 20240106135321.png)
ya estamos dentro

### En la ruta /var/www/html podemos conseguir un archivo de configuracion que contiene credenciales

![img](/assets/img/imagenes/Pasted image 20240106140658.png)

### Escalada de privilegios 

![img](/assets/img/imagenes/Pasted image 20240106141104.png)
el usuario c0ldd puede ejecutar los siguientes comandos como root....
usaremos el comando chmod para cambiar los permisos de la bash y asi poder spaunear una bash como root

![img](/assets/img/imagenes/Pasted image 20240106141327.png)
### WE ARE ROOT