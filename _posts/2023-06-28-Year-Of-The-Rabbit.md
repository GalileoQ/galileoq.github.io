---
title: "Year-Of-The-Rabbit Writeup - Try-Hack-Me"
date: 2023-06-28
categories: [Writeups, Try-Hack-Me]
tags: [Linux, Easy]
image: 
  path: assets/img/imagenes/Year-of-the-Rabbit.jpeg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Year-Of-The-Rabbit banner"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### nmap

```python
sudo nmap -p- --open -sS -sCV -n -Pn 10.10.65.163
[sudo] password for kali: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-16 20:52 EST
Nmap scan report for 10.10.65.163
Host is up (0.22s latency).
Not shown: 65477 closed tcp ports (reset), 55 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
|_  256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.10 (Debian)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 85.76 seconds
```
### ports: 21/ftp - 22/ssh - 80/http

### empezamos enumerando el puerto 80
![img](/assets/img/imagenes/Pasted image 20231116215827.png)
al parecer no hay mucho en la pagina web. 

### aplicaremos fuzzing utilizando gobuster
![img](/assets/img/imagenes/Pasted image 20231116215958.png)
podemos enumerar un directorio llamado /assets asi que vamos a investigar
![img](/assets/img/imagenes/Pasted image 20231116220255.png)
tenemos un archivo .mp4 que no parece ser importante. y un archivo llamado style.css. si miramos dentro del el vemos algo interesante. 
![img](/assets/img/imagenes/Pasted image 20231116220502.png)

### tenemos una nota que nos sugiere una direccion asi que vamos a investigar
![img](/assets/img/imagenes/Pasted image 20231116220645.png)

### tenemos mensaje en pantalla que nos sugiere desactivar el javascript
al parecer desactivar el javascript no funciona debido a que nos sigue redirigiendo al .mp4 que hemos conseguido anteriormente.

### vamos a interceptar esta peticion con burpsuite
![img](/assets/img/imagenes/Pasted image 20231116222108.png)

### al parecer burpsuite nos dice que se esta aplicando otra redireccion asi que vamos a investigar.
al parecer burpsuite sugiere dos rutas de redireccion
![img](/assets/img/imagenes/Pasted image 20231116222553.png)

### hemos conseguido un direcctorio oculto. vamos a investigar
![img](/assets/img/imagenes/Pasted image 20231116222644.png)
solo podemos conseguir una imagen. la descargaremos a nuestro equipo de trabajo para intentar conseguir algún tipo de información oculta 

### Recopilación de información oculta
![img](/assets/img/imagenes/Pasted image 20231116223122.png)

### al parecer la imagen esta encriptada. necesitaremos desencriptarla para poder ver la informacion
probamos con la herramienta strings
```python
strings archivo
```

### tenemos una nota que nos da informacion sobre el usuario para el servicio ftp y nos dice que la contraseña puede ser una de las siguientes
![img](/assets/img/imagenes/Pasted image 20231116224608.png)

### vamos a crear un pequeño diccionario con estas contraseñas y lo usaremos para aplicarle fuerza bruta al puerto ftp
![img](/assets/img/imagenes/Pasted image 20231116230935.png)

### tenemos credenciales para el puerto ftp
```python
	[21]ftp: host: 10.10.113.73 login: ftpuser password: 5iez1wGXKfPKQ
```
![img](/assets/img/imagenes/Pasted image 20231116231341.png)

### tenemos conexion por el puerto ftp. vamos a enumerar
![img](/assets/img/imagenes/Pasted image 20231116231621.png)

### hemos conseguido un archivo. con el comando `mget *`  podemos descargarnos todo.
![img](/assets/img/imagenes/Pasted image 20231116231826.png)

### al parecer el archivo esta encriptado de alguna manera. tendremos que buscar la forma de desencriptarlo
![img](/assets/img/imagenes/Pasted image 20231116232517.png)

### haciendo una busqueda en internet al parecer el codigo que vemos es un codigo de programacion llamado Brainfuck. asi que buscaremos alguna manera de desencriptar este codigo ya que parece que esta ofuscado
![img](/assets/img/imagenes/Pasted image 20231116232700.png)

### tenemos nuevas credenciales. nos falta enumerar el puerto ssh asi que probablemente estas sean las credenciales para dicho puerto.
![img](/assets/img/imagenes/Pasted image 20231116232954.png)

### tenemos un mensaje para Gwendoline que dice lo siguiente
```python
1 mensaje nuevo
Mensaje de Root a Gwendoline:

"Gwendoline, no estoy contento contigo. Revisa nuestro escondite de leet s3cr3t. Te dejé un mensaje oculto allí".

FINALIZAR MENSAJE
```
al parecer tenemos otro mensaje oculto en algun lugar llamado leet s3cr3t

### buscaremos el direcctorio "s3cr3t" con el comando
```python
	find / -name "s3cr3t" 2>/dev/null
```
![img](/assets/img/imagenes/Pasted image 20231117000310.png)
tenemos otro archivo oculto
![img](/assets/img/imagenes/Pasted image 20231117000435.png)
hacemos un cat de este archivo oculto que hemos encontrado
![img](/assets/img/imagenes/Pasted image 20231117000614.png)

### tenemos un nuevo mensaje con lo que parece ser credenciales del usuario Gwendoline
```python
Tu contraseña es horrible, Gwendoline.
¡Debe tener al menos 60 caracteres! No sólo MniVCQVhQHUNI
¡Honestamente!

Tuyo sinceramente
```
### cambiamos de usuario
```python
	su gwendoline
	passwd: MniVCQVhQHUNI
```
### tenemos conexion somos el usuario gwendoline. nos falta escalar privilegios.
![img](/assets/img/imagenes/Pasted image 20231117003332.png)
###### tenemos permisos de root para ejecutar el binario /usr/bin/vi sobre la ruta /home/gwendoline/user.txt
aprovecharemos esto para escalar privilegios utilizando el comando vi para editar el archivo user.txt y poder espaunear una bash como root

### Utilizaremos el siguiente comando 
```python
	sudo -u#-1

	-u#-1

	con este comando le indicamos que al usuario actual le vamos a restar 1 ( -1 ) lo que nos dara como resultado 0 y el numero 0 es el usuario root, asi que aprovecharemos esto
```
### escalada de privilegios
![img](/assets/img/imagenes/Pasted image 20231117004753.png)

### con esto usamos el binario /usr/bin/vi sobre la ruta /home/gwendoline/user.txt restando -1 al usuario actual lo que permite que se ejecute como root
![img](/assets/img/imagenes/Pasted image 20231117004550.png)

### aqui precionamos `shift !` varias veces para poder irnos a la parte de abajo donde podremos eliminar ese . y despues inyectar codigo para espaunear una bash
![img](/assets/img/imagenes/Pasted image 20231117005208.png)
nos queda algo asi
![img](/assets/img/imagenes/Pasted image 20231117005345.png)

### WE ARE ROOT