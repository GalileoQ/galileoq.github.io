---
title: "Git-Happens Writeup - Try-Hack-Me"
date: 2023-10-06
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets\img\imagenes\Git-Happens.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Git-Happens banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### nmap

```python
sudo nmap -sCV 10.10.46.137
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-12 20:48 EST
Nmap scan report for 10.10.46.137
Host is up (0.21s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/fuel/
|_http-title: Welcome to FUEL CMS
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.78 seconds
```

### enumeración de pag web
![img](/assets/img/imagenes/Pasted image 20231112215229.png)

##### tenemos el framework y la version 1.4
### gobuster
![img](/assets/img/imagenes/Pasted image 20231112215807.png)
##### tenemos un archivo robots.txt

### revisando la pagina principal nos da informacion sobre las credenciales por defecto para el panel de administracion
![img](/assets/img/imagenes/Pasted image 20231112220116.png)
usamos las credenciales
![img](/assets/img/imagenes/Pasted image 20231112220326.png)

### buscamos en Internet y conseguimos un exploit que nos permite ejecución de datos
![img](/assets/img/imagenes/Pasted image 20231112230624.png)

### ejecutamos. el exploit nos permite ejecutar comandos así que le lanzaremos una bash y estaremos a la escucha
![img](/assets/img/imagenes/Pasted image 20231112231017.png)

### enumeramos todas las carpetas hasta llegar a la ruta /var/www/html/fuel/application 
![img](/assets/img/imagenes/Pasted image 20231112231545.png)

### tenemos un archivo database.php
![img](/assets/img/imagenes/Pasted image 20231112231754.png)

### tenemos credenciales para root.
![img](/assets/img/imagenes/Pasted image 20231112232022.png)

### WE ARE ROOT